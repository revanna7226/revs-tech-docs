Builder
=======

When construction gets a little bit too complicated.

.. admonition:: Definition

    When piece-wise object construction is complicated, provide an API for doing it succinctly.

Motivation
----------  
- Some objects are simple and can be created in a single constructor call
- Other Objects requires a lot of ceremony to create
- Having an Object with 10 constructor arguments is not productive
- Instead opt for piece-wise construction.
- Builder provides an API for constructing an object step-by-step

Builders in Java
----------------

.. code-block:: Java

    package com.revs.designpatterns;

    import java.util.ArrayList;
    import java.util.Collections;

    class HtmlElement {

        public String name, text;
        public ArrayList<HtmlElement> elements = new ArrayList<HtmlElement>();
        private final int indentSize = 2;
        private final String newLine = System.lineSeparator();

        public HtmlElement() {
        }

        public HtmlElement(String name, String text) {
            this.name = name;
            this.text = text;
        }

        private String toStringImpl(int indent) {
            StringBuilder sb = new StringBuilder();
            String i = String.join("", Collections.nCopies(indent * indentSize, " "));
            sb.append(String.format("%s<%s>%s", i, name, newLine));
            if (text != null && !text.isEmpty()) {
                sb.append(String.join("", Collections.nCopies(indentSize * (indent + 1), " "))).append(text).append(newLine);
            }

            for (HtmlElement e : elements)
                sb.append(e.toStringImpl(indent + 1));

            sb.append(String.format("%s</%s>%s", i, name, newLine));
            return sb.toString();
        }

        @Override
        public String toString() {
            return toStringImpl(0);
        }
    }

    class HtmlBuilder {

        private String rootName;
        private HtmlElement root = new HtmlElement();

        public HtmlBuilder(String rootName) {
            this.rootName = rootName;
            root.name = rootName;
        }

        // not fluent
        public void addChild(String childName, String childText) {
            HtmlElement e = new HtmlElement(childName, childText);
            root.elements.add(e);
        }

        public HtmlBuilder addChildFluent(String childName, String childText) {
            HtmlElement e = new HtmlElement(childName, childText);
            root.elements.add(e);
            return this;
        }

        public void clear() {
            root = new HtmlElement();
            root.name = rootName;
        }

        // delegating
        @Override
        public String toString() {
            return root.toString();
        }
    }

    class Builder {
        public static void main(String[] args) {
            // we want to build a simple HTML paragraph
            System.out.println("Testing");
            String hello = "hello";
            System.out.println("<p>" + hello + "</p>");

            // now we want to build a list with 2 words
            String[] words = {"hello", "world"};
            StringBuilder sb = new StringBuilder();
            sb.append("<ul>\n");
            for (String word : words) {
                // indentation management, line breaks and other evils
                sb.append(String.format("  <li>%s</li>\n", word));
            }
            sb.append("</ul>");
            System.out.println(sb);

            // ordinary non-fluent builder
            HtmlBuilder builder = new HtmlBuilder("ul");
            builder.addChild("li", "hello");
            builder.addChild("li", "world");
            System.out.println(builder);

            // fluent builder
            builder.clear();
            builder.addChildFluent("li", "hello").addChildFluent("li", "world");
            System.out.println(builder);
        }
    }



Fluent Builder Inheritance with Recursive Generics
--------------------------------------------------

Sometimes Builder is inherit from another Builder.

.. code-block:: java

    package com.revs.designpatterns;

    // builder inheritance with recursive generics
    class Person {
        public String name;

        public String position;

        @Override
        public String toString() {
            return "Person{" +
                    "name='" + name + '\'' +
                    ", position='" + position + '\'' +
                    '}';
        }
    }

    class PersonBuilder<SELF extends PersonBuilder<SELF>> {
        protected Person person = new Person();

        // critical to return SELF here
        public SELF withName(String name) {
            person.name = name;
            return self();
        }

        protected SELF self() {
            // unchecked cast, but actually safe
            // proof: try sticking a non-PersonBuilder
            //        as SELF parameter; it won't work!
            return (SELF) this;
        }

        public Person build() {
            return person;
        }
    }

    class EmployeeBuilder
            extends PersonBuilder<EmployeeBuilder> {
        public EmployeeBuilder worksAs(String position) {
            person.position = position;
            return self();
        }

        @Override
        protected EmployeeBuilder self() {
            return this;
        }
    }

    class BuilderRecursiveGenerics {
        public static void main(String[] args) {
            EmployeeBuilder eb = new EmployeeBuilder()
                    .withName("Dmitri")
                    .worksAs("Quantitative Analyst");
            System.out.println(eb.build());
        }
    }


Faceted Builder
---------------

.. code-block:: java

    package com.revs.designpatterns.builder;

    class Person {
        // address
        public String streetAddress, postcode, city;

        // employment
        public String companyName, position;
        public int annualIncome;

        @Override
        public String toString() {
            return "Person{" +
                    "streetAddress='" + streetAddress + '\'' +
                    ", postcode='" + postcode + '\'' +
                    ", city='" + city + '\'' +
                    ", companyName='" + companyName + '\'' +
                    ", position='" + position + '\'' +
                    ", annualIncome=" + annualIncome +
                    '}';
        }
    }

    // builder facade
    class PersonBuilder {
        // the object we're going to build
        protected Person person = new Person(); // reference!

        public PersonJobBuilder works() {
            return new PersonJobBuilder(person);
        }

        public PersonAddressBuilder lives() {
            return new PersonAddressBuilder(person);
        }

        public Person build() {
            return person;
        }
    }

    class PersonAddressBuilder extends PersonBuilder {
        public PersonAddressBuilder(Person person) {
            this.person = person;
        }

        public PersonAddressBuilder at(String streetAddress) {
            person.streetAddress = streetAddress;
            return this;
        }

        public PersonAddressBuilder withPostcode(String postcode) {
            person.postcode = postcode;
            return this;
        }

        public PersonAddressBuilder in(String city) {
            person.city = city;
            return this;
        }
    }

    class PersonJobBuilder extends PersonBuilder {
        public PersonJobBuilder(Person person) {
            this.person = person;
        }

        public PersonJobBuilder at(String companyName) {
            person.companyName = companyName;
            return this;
        }

        public PersonJobBuilder asA(String position) {
            person.position = position;
            return this;
        }

        public PersonJobBuilder earning(int annualIncome) {
            person.annualIncome = annualIncome;
            return this;
        }
    }

    class FacetedBuilder {
        public static void main(String[] args) {
            PersonBuilder pb = new PersonBuilder();
            Person person = pb
                    .lives()
                    .at("123 London Road")
                    .in("London")
                    .withPostcode("SW12BC")
                    .works()
                    .at("Fabrikam")
                    .asA("Engineer")
                    .earning(123000)
                    .build();
            System.out.println(person);
        }
    }

Excercise: Builder Coding Exercise
-----------------------------------

You are asked to implement the Builder design pattern for rendering simple chunks of code.

Sample use of the builder you are asked to create:

.. code-block:: java

    CodeBuilder cb = new CodeBuilder("Person").addField("name", "String").addField("age", "int");
    System.out.println(cb);


The expected output of the above code is:

.. code-block:: java

    public class Person
    {
      public String name;
      public int age;
    }

Please observe the same placement of curly braces and use two-space indentation.

Solution
--------

.. code-block:: java

    package com.activemesa.creational.builder.exercise;

    import org.junit.Test;
    import org.junit.Assert;

    import java.util.ArrayList;
    import java.util.List;

    class Field {
        public String type, name;

        public Field(String name, String type) {
            this.type = type;
            this.name = name;
        }

        @Override
        public String toString() {
            return String.format("public %s %s;", type, name);
        }
    }

    class Class {
        public String name;
        public List<Field> fields = new ArrayList<>();

        public Class() {
        }

        @Override
        public String toString() {
            StringBuilder sb = new StringBuilder();
            String nl = System.lineSeparator();
            sb.append("public class " + name).append(nl)
                    .append("{").append(nl);
            for (Field f : fields)
                sb.append("  " + f).append(nl);
            sb.append("}").append(nl);
            return sb.toString();
        }
    }

    class CodeBuilder {
        private Class theClass = new Class();

        public CodeBuilder(String rootName) {
            theClass.name = rootName;
        }

        public CodeBuilder addField(String name, String type) {
            theClass.fields.add(new Field(name, type));
            return this;
        }

        @Override
        public String toString() {
            return theClass.toString();
        }
    }

    //import org.junit.Test;
    //import org.junit.Assert;
    //import com.udemy.ucp.*;

Tests
-----

.. code-block:: java

    package com.activemesa.creational.builder.exercise;

    import org.junit.Assert;
    import org.junit.Test;

    import static org.junit.Assert.assertEquals;

    public class Evaluate {
        private String preprocess(String text) {
            return text.replace("\r\n", "\n").trim();
        }

        @Test
        public void emptyTest() {
            CodeBuilder cb = new CodeBuilder("Foo");
            assertEquals("public class Foo\n{\n}",
                    preprocess(cb.toString()));
        }

        @Test
        public void personTest() {
            CodeBuilder cb = new CodeBuilder("Person")
                    .addField("name", "String")
                    .addField("age", "int");
            assertEquals("public class Person\n{\n" +
                            "  public String name;\n" +
                            "  public int age;\n}",
                    preprocess(cb.toString()));
        }
    }

Summary
-------

  - A builder is a separate component for building an object

  - so Can either give builder a constructor or return it via a static function

  - To make builder fluent, return this

  - Different facets of an object can be built with different builders working in tandem via a base class

