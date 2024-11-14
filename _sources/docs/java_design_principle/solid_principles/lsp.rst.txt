3. Linkov Substitution Principle - LSP
======================================

The Liskov Substitution Principle states: “If S is a subtype of T, then objects of type T may be replaced with objects of type > S without altering any of the desirable properties of the program (correctness, task performed, etc.).”

In simpler terms, any implementation of an abstraction (interface) should be substitutable anywhere that abstraction is accepted.

Reference: https://medium.com/@ahmedtahaelelemy/understanding-the-liskov-substitution-principle-a-deep-dive-into-solid-principles-b02ac6a18ee3

.. code-block:: java

    package com.activemesa.solid.lsp;

    class Rectangle
    {
    protected int width, height;

    public Rectangle() {
    }

    public Rectangle(int width, int height) {
        this.width = width;
        this.height = height;
    }

    public int getWidth() {
        return width;
    }

    public void setWidth(int width) {
        this.width = width;
    }

    public int getHeight() {
        return height;
    }

    public void setHeight(int height) {
        this.height = height;
    }

    public int getArea() { return width*height; }

    @Override
    public String toString() {
        return "Rectangle{" +
        "width=" + width +
        ", height=" + height +
        '}';
    }

    public boolean isSquare()
    {
        return width == height;
    }
    }

    class Square extends Rectangle
    {
    public Square() {
    }

    public Square(int size) {
        width = height = size;
    }

    @Override
    public void setWidth(int width) {
        super.setWidth(width);
        super.setHeight(width);
    }

    @Override
    public void setHeight(int height) {
        super.setHeight(height);
        super.setWidth(height);
    }
    }

    class RectangleFactory
    {
    public static Rectangle newSquare(int side)
    {
        return new Rectangle(side, side);
    }

    public static Rectangle newRectangle(int width, int height)
    {
        return new Rectangle(width, height);
    }
    }

    class LSPDemo
    {
    // maybe conform to ++
    static void useIt(Rectangle r)
    {
        int width = r.getWidth();
        r.setHeight(10);
        System.out.println("Expected area of " + (width*10) + ", got " + r.getArea());
    }

    public static void main(String[] args) {
        Rectangle rc = new Rectangle(2, 3);
        useIt(rc);

        Rectangle sq = new Square();
        sq.setHeight(5);
        sq.setWidth(10);
        useIt(sq);
    }
    }
