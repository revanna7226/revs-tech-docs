Auditing with Spring Security
===============================================

Spring Data JPA provides a lightweight auditing facility that can automatically populate
common auditing properties on entities — for example who created or last modified an entity,
and when that happened. When combined with Spring Security, you can automatically record
the currently authenticated user as the auditor.

This document explains how to enable and configure Spring Data JPA auditing together with
Spring Security, gives multiple implementation patterns (storing username or user id),
and shows examples of configuration, entities, testing, and best practices.

Contents:
    - Enabling auditing
    - Auditing annotations (fields)
    - Implementing `AuditorAware<T>`
        - Returning username (`String`)
        - Returning user id (`Long`) by reading custom `UserDetails`
    - Configuration examples (modern Spring Security)
    - Example entity and repository
    - Testing auditing with Spring Security
    - Best practices and caveats

#. **Enabling auditing:**

    To turn on Spring Data JPA auditing, register `@EnableJpaAuditing` in a `@Configuration`
    class or your `@SpringBootApplication`. If you use a custom `AuditorAware` bean, reference it
    with the `auditorAwareRef` attribute.

    .. code-block:: java

        @Configuration
        @EnableJpaAuditing(auditorAwareRef = "auditorAware")
        public class JpaAuditingConfig {
            // AuditorAware bean declared elsewhere (or below)
        }

#. **Auditing annotations (fields):**

    Spring Data JPA provides the following annotations that you can add to entity fields:

    - ``@CreatedBy`` — populated with the current auditor when the entity is first persisted.
    - ``@LastModifiedBy`` — populated with the current auditor when the entity is updated.
    - ``@CreatedDate`` — populated with a timestamp when the entity is created.
    - ``@LastModifiedDate`` — populated with a timestamp when the entity is updated.

    Example field types that work for dates: ``java.time.Instant``, ``java.time.LocalDateTime``,
    ``java.time.OffsetDateTime``. For auditor fields you can use ``String`` (username), ``Long``
    (user id), or a custom type matching your domain.

#. **Implementing AuditorAware<T>:**

    `AuditorAware<T>` is a functional interface that must return an ``Optional<T>`` with the
    current principal (auditor). Typical choices for ``T`` are ``String`` (username) or ``Long``
    (user id). The implementation should fetch the currently authenticated principal from
    ``SecurityContextHolder`` (or from a reactive security context in a reactive application).

    Below are two common implementations.

    #. **AuditorAware<String> — return username:**

        .. code-block:: java

            import org.springframework.data.domain.AuditorAware;
            import org.springframework.security.core.Authentication;
            import org.springframework.security.core.context.SecurityContextHolder;

            import java.util.Optional;

            public class SpringSecurityAuditorAware implements AuditorAware<String> {

                @Override
                public Optional<String> getCurrentAuditor() {
                    Authentication auth = SecurityContextHolder.getContext().getAuthentication();
                    if (auth == null || !auth.isAuthenticated()) {
                        return Optional.empty();
                    }
                    Object principal = auth.getPrincipal();
                    if (principal instanceof org.springframework.security.core.userdetails.UserDetails) {
                        return Optional.of(((org.springframework.security.core.userdetails.UserDetails) principal).getUsername());
                    }
                    // principal can be a String in some configurations
                    if (principal instanceof String) {
                        return Optional.of((String) principal);
                    }
                    return Optional.empty();
                }
            }

        Register this bean and reference it in ``@EnableJpaAuditing``.

        .. code-block:: java

            @Configuration
            @EnableJpaAuditing(auditorAwareRef = "auditorAware")
            public class JpaAuditingConfig {

                @Bean
                public AuditorAware<String> auditorAware() {
                    return new SpringSecurityAuditorAware();
                }
            }

    #. **AuditorAware<Long> — return user id using a custom UserDetails:**

        If your application tracks users by numeric id in the database, you usually implement a
        custom ``UserDetails`` (or extend an existing one) to expose the id, and convert it in the
        ``AuditorAware``:

        .. code-block:: java

            // Domain user (example)
            public class AppUser {
                private Long id;
                private String username;
                // getters/setters
            }

            // Custom UserDetails exposing id
            import org.springframework.security.core.GrantedAuthority;
            import org.springframework.security.core.userdetails.UserDetails;
            import java.util.Collection;

            public class CustomUserDetails implements UserDetails {
                private final Long id;
                private final String username;
                private final String password;
                private final Collection<? extends GrantedAuthority> authorities;

                public CustomUserDetails(Long id, String username, String password, Collection<? extends GrantedAuthority> authorities) {
                    this.id = id;
                    this.username = username;
                    this.password = password;
                    this.authorities = authorities;
                }

                public Long getId() { return id; }

                @Override public String getUsername() { return username; }
                @Override public String getPassword() { return password; }
                @Override public Collection<? extends GrantedAuthority> getAuthorities() { return authorities; }
                @Override public boolean isAccountNonExpired() { return true; }
                @Override public boolean isAccountNonLocked() { return true; }
                @Override public boolean isCredentialsNonExpired() { return true; }
                @Override public boolean isEnabled() { return true; }
            }

            // AuditorAware that returns Long id
            import org.springframework.data.domain.AuditorAware;
            import org.springframework.security.core.Authentication;
            import org.springframework.security.core.context.SecurityContextHolder;

            import java.util.Optional;

            public class JpaAuditorAwareById implements AuditorAware<Long> {

                @Override
                public Optional<Long> getCurrentAuditor() {
                    Authentication auth = SecurityContextHolder.getContext().getAuthentication();
                    if (auth == null || !auth.isAuthenticated()) {
                        return Optional.empty();
                    }
                    Object principal = auth.getPrincipal();
                    if (principal instanceof CustomUserDetails) {
                        return Optional.of(((CustomUserDetails) principal).getId());
                    }
                    return Optional.empty();
                }
            }

        Register bean similarly:

        .. code-block:: java

            @Configuration
            @EnableJpaAuditing(auditorAwareRef = "auditorAware")
            public class JpaAuditingConfig {

                @Bean
                public AuditorAware<Long> auditorAware() {
                    return new JpaAuditorAwareById();
                }
            }

#. **Configuration examples (modern Spring Security):**

    Spring Security's recommended configuration style (since 5.7) is to expose a ``SecurityFilterChain`` bean.
    Below is a minimal example that uses an in-memory user for demonstration. In a real app you would
    load users from your database or an external provider and return a ``CustomUserDetails`` with id.

    .. code-block:: java

        import org.springframework.context.annotation.Bean;
        import org.springframework.context.annotation.Configuration;
        import org.springframework.security.config.annotation.method.configuration.EnableMethodSecurity;
        import org.springframework.security.config.annotation.web.builders.HttpSecurity;
        import org.springframework.security.core.userdetails.User;
        import org.springframework.security.core.userdetails.UserDetailsService;
        import org.springframework.security.provisioning.InMemoryUserDetailsManager;
        import org.springframework.security.web.SecurityFilterChain;

        @Configuration
        @EnableMethodSecurity
        public class SecurityConfig {

            @Bean
            public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
                http
                    .csrf().disable()
                    .authorizeHttpRequests(auth -> auth
                        .anyRequest().authenticated()
                    )
                    .httpBasic();
                return http.build();
            }

            @Bean
            public UserDetailsService userDetailsService() {
                // NOTE: demo uses username/password strings. For id-based auditing
                // implement and return your custom UserDetailsService that loads CustomUserDetails.
                return new InMemoryUserDetailsManager(
                    User.withDefaultPasswordEncoder()
                        .username("alice")
                        .password("password")
                        .roles("USER")
                        .build()
                );
            }
        }

#. **Example entity and repository:**

    Example using username (String) as auditor and ``Instant`` for dates:

    .. code-block:: java

        import org.springframework.data.annotation.CreatedBy;
        import org.springframework.data.annotation.CreatedDate;
        import org.springframework.data.annotation.LastModifiedBy;
        import org.springframework.data.annotation.LastModifiedDate;
        import org.springframework.data.jpa.domain.support.AuditingEntityListener;

        import javax.persistence.*;
        import java.time.Instant;

        @Entity
        @EntityListeners(AuditingEntityListener.class)
        public class Document {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String title;

            @CreatedBy
            private String createdBy;

            @CreatedDate
            private Instant createdDate;

            @LastModifiedBy
            private String lastModifiedBy;

            @LastModifiedDate
            private Instant lastModifiedDate;

            // getters & setters
        }

    Repository:

    .. code-block:: java

        import org.springframework.data.jpa.repository.JpaRepository;

        public interface DocumentRepository extends JpaRepository<Document, Long> {
        }

    Important: make sure you annotate your entity with ``@EntityListeners(AuditingEntityListener.class)`` or enable JPA's auditing processor globally; Spring will then fill the annotated fields automatically when the entity is persisted/updated.

#. **Testing auditing with Spring Security:**

    You can test auditing behavior in Spring Boot tests by populating the `SecurityContext`. For unit tests, use ``@WithMockUser`` or set the ``SecurityContextHolder`` manually.

    Example using ``@WithMockUser`` (JUnit + Spring Test):

    .. code-block:: java

        import org.junit.jupiter.api.Test;
        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
        import org.springframework.security.test.context.support.WithMockUser;
        import java.util.Optional;

        @DataJpaTest
        public class DocumentAuditingTest {

            @Autowired
            private DocumentRepository repository;

            @Test
            @WithMockUser(username = "testuser")
            void whenSave_thenCreatedByIsSet() {
                Document d = new Document();
                d.setTitle("Hello");
                Document saved = repository.save(d);

                assertNotNull(saved.getCreatedDate());
                assertEquals("testuser", saved.getCreatedBy());
            }
        }

    If you rely on a numeric id as auditor, use a custom test configuration to populate the ``Authentication`` principal with a ``CustomUserDetails`` instance that has an id.

#. Edge cases and caveats:
    - **Anonymous or unauthenticated requests**: ``AuditorAware`` should return ``Optional.empty()`` when unauthenticated. You can decide to provide a fallback value (e.g., ``"system"``).
    - **Transactions & EntityManager flush**: Auditing fields are set during persist/merge operations. If you manipulate entities after the save but before commit, auditing timestamps/values are applied by the persistence provider.
    - **DTOs vs Entities**: Auditing applies only to JPA-managed entities. If you use DTO inserts with native queries, auditing won't run automatically.
    - **Database triggers**: If the database also sets timestamps or user columns via triggers, be careful about conflicts — prefer one source of truth.
    - **Reactive applications**: Use the reactive security context (``ReactiveSecurityContextHolder``) and the reactive variant of ``AuditorAware`` if you work on a reactive stack.
    - **Lazy Loading & Serialization**: If auditor fields reference entity relationships rather than simple scalars, be cautious about lazy-loading during serialization.

#. Advanced topics:
    - **Date provider customization**: By default, auditing uses the system clock. You can provide a custom ``DateTimeProvider`` bean to control how dates are created (useful for tests or legacy calendar systems).
    - **Using entity references for auditor**: Instead of storing a scalar (String/Long) you can store a relationship to a ``User`` entity. This requires the auditor-aware to return the appropriate entity or id and map the entity correctly — but it increases coupling and complexity (avoid unless necessary).
    - **Auditing nested/embedded entities**: Auditing annotations apply to fields on the root entity. For embedded types, ensure the embeddable contains the auditing fields and entity listeners are present.

#. Putting it all together (complete minimal example):

    .. code-block:: java

        // 1) AuditorAware that returns username
        @Bean
        public AuditorAware<String> auditorAware() {
            return () -> {
                Authentication auth = SecurityContextHolder.getContext().getAuthentication();
                if (auth == null || !auth.isAuthenticated()) {
                    return Optional.empty();
                }
                Object principal = auth.getPrincipal();
                if (principal instanceof UserDetails) {
                    return Optional.of(((UserDetails) principal).getUsername());
                } else if (principal instanceof String) {
                    return Optional.of((String) principal);
                }
                return Optional.empty();
            };
        }

        // 2) Enable auditing
        @Configuration
        @EnableJpaAuditing(auditorAwareRef = "auditorAware")
        public class JpaAuditingConfig {}

        // 3) Entity (see Document example above)

        // 4) Simple Security configuration (see SecurityConfig above)

Best practices:
    - Use ``String`` (username/email) for auditors if you don't need to join auditor fields to your user table.
    - Use numeric id (``Long``) only if you need referential integrity to the user table — but prefer a simple scalar column for audit readability.
    - Implement ``equals()`` and ``hashCode()`` on complex auditor objects only if you are storing objects as references.
    - Prefer ``Instant`` for timestamps to avoid timezone confusion and to be compatible with modern best practices.
    - For testing, always ensure the security context is populated with a principal; tests that forget this might observe ``null``/``empty`` audit fields.

References:
    - Spring Data JPA — Auditing (see Spring Data reference for full detail)
    - Spring Security reference (for Authentication and custom UserDetails patterns)

Acknowledgements:
    This guide uses the modern Spring Security configuration approach (``SecurityFilterChain``)
    and the Spring Data JPA ``AuditorAware`` extension point to glue authentication to auditing.
