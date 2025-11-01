3. Linkov Substitution Principle - LSP
======================================

Objects of a superclass should be replaceable with objects of its subclasses without affecting the correctness of the program.

.. code-block:: java

    abstract class Payment {
        abstract void processPayment(double amount);
    }

    class CreditCardPayment extends Payment {
        @Override
        void processPayment(double amount) {
            System.out.println("Processing credit card payment of $" + amount);
        }
    }

    class PayPalPayment extends Payment {
        @Override
        void processPayment(double amount) {
            System.out.println("Processing PayPal payment of $" + amount);
        }
    }

    // Client code
    void process(Payment payment, double amount) {
        payment.processPayment(amount);  // Works with any Payment subclass
    }
    