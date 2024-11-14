Builder
=======

When construction gets a little bit too complicated.

.. admonition:: Definition

    When piece-wise object construction is complicated, provide an API for doing it succinctly.


- Some objects are simple and can be created in a single constructor call
- Other Objects requires a lot of ceremony to create
- Having an Object with 10 constructor arguments is not productive
- Instead opt for piece-wise construction.
- Builder provides an API for constructing an object step-by-step

Builders in Java
----------------

.. code-block:: Java

    package com.activemesa.creational.builder;

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
            if (text != null && !text.isEmpty())
            {
            sb.append(String.join("", Collections.nCopies(indentSize*(indent+1), " ")))
                .append(text)
                .append(newLine);
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

    class BuilderDemo {
        public static void main(String[] args) {
            // we want to build a simple HTML paragraph
            System.out.println("Testing");
            String hello = "hello";
            System.out.println("<p>" + hello + "</p>");

            // now we want to build a list with 2 words
            String [] words = {"hello", "world"};
            StringBuilder sb = new StringBuilder();
            sb.append("<ul>\n");
            for (String word: words)
            {
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
            builder.addChildFluent("li", "hello")
            .addChildFluent("li", "world");
            System.out.println(builder);
        }
    }


Fluent Builder Inheritance with Recursive Generics
--------------------------------------------------

Sometimes Builder is inherit from another Builder.

.. code-block:: java

    package com.activemesa.creational.builder;

    // builder inheritance with recursive generics

    class Person
    {
    public String name;

    public String position;

    @Override
    public String toString()
    {
        return "Person{" +
        "name='" + name + '\'' +
        ", position='" + position + '\'' +
        '}';
    }
    }

    class PersonBuilder<SELF extends PersonBuilder<SELF>>
    {
    protected Person person = new Person();

    // critical to return SELF here
    public SELF withName(String name)
    {
        person.name = name;
        return self();
    }

    protected SELF self()
    {
        // unchecked cast, but actually safe
        // proof: try sticking a non-PersonBuilder
        //        as SELF parameter; it won't work!
        return (SELF) this;
    }

    public Person build()
    {
        return person;
    }
    }

    class EmployeeBuilder
    extends PersonBuilder<EmployeeBuilder>
    {
    public EmployeeBuilder worksAs(String position)
    {
        person.position = position;
        return self();
    }

    @Override
    protected EmployeeBuilder self()
    {
        return this;
    }
    }

    class RecursiveGenericsDemo
    {
    public static void main(String[] args)
    {
        EmployeeBuilder eb = new EmployeeBuilder()
        .withName("Dmitri")
        .worksAs("Quantitative Analyst");
        System.out.println(eb.build());
    }
    }
