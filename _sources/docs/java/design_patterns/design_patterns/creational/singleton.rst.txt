Singleton
=========

Motivation
----------

For some components it only makes sense to have one in the system

- Database repository
- Object factory

E.g., the constructor call is expensive

- We only do it once
- We provide everyone with the same instance

Want to prevent anyone creating additional copies

Need to take care of lazy instantiation and thread safety


.. admonition:: Definition

    A Component which is instantiated only once.

Basic Singleton and Serialization Promblems
-------------------------------------------

.. code-block:: java

    package com.revs.designpatterns.singleton;

    import java.io.*;

    class BasicSingleton implements Serializable {
        // cannot new this class, however
        // * instance can be created deliberately (reflection)
        // * instance can be created accidentally (serialization)
        private BasicSingleton() {
            System.out.println("Singleton is initializing");
        }

        private static final BasicSingleton INSTANCE = new BasicSingleton();

        private int value = 0;

        public int getValue() {
            return value;
        }

        public void setValue(int value) {
            this.value = value;
        }

        // required for correct serialization
        // readResolve is used for _replacing_ the object read from the stream

    //  protected Object readResolve()
    //  {
    //    return INSTANCE;
    //  }

        // generated getter
        public static BasicSingleton getInstance() {
            return INSTANCE;
        }
    }

    class BasicSingletonDemo {
        static void saveToFile(BasicSingleton singleton, String filename)
                throws Exception {
            try (FileOutputStream fileOut = new FileOutputStream(filename);
                ObjectOutputStream out = new ObjectOutputStream(fileOut)) {
                out.writeObject(singleton);
            }
        }

        static BasicSingleton readFromFile(String filename)
                throws Exception {
            try (FileInputStream fileIn = new FileInputStream(filename);
                ObjectInputStream in = new ObjectInputStream(fileIn)) {
                return (BasicSingleton) in.readObject();
            }
        }

        public static void main(String[] args) throws Exception {
            BasicSingleton singleton = BasicSingleton.getInstance();
            singleton.setValue(111);

            String filename = "singleton.bin";
            saveToFile(singleton, filename);

            singleton.setValue(222);

            BasicSingleton singleton2 = readFromFile(filename);

            System.out.println(singleton == singleton2);

            System.out.println(singleton.getValue());
            System.out.println(singleton2.getValue());
        }
    }

Static Block Singleton
----------------------

.. code-block:: java

    package com.revs.designpatterns.singleton;

    import java.io.File;
    import java.io.IOException;

    class StaticBlockSingleton {
        private StaticBlockSingleton() throws IOException {
            System.out.println("Initializing Static block singleton");
            File.createTempFile(".", ".");
        }

        private static StaticBlockSingleton instance;

        static {
            try {
                instance = new StaticBlockSingleton();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }

        public static StaticBlockSingleton getInstance() {
            return instance;
        }

        public int value;

        public int getValue() {
            return value;
        }

        public void setValue(int value) {
            this.value = value;
        }
    }

    public class StaticBlockSingletonDemo {
        public static void main(String[] args) {
            StaticBlockSingleton staticBlockSingleton = StaticBlockSingleton.getInstance();
            staticBlockSingleton.setValue(100);
            System.out.println(staticBlockSingleton);
        }
    }

Lazyness and Thread safety Singleton
-------------------------------------

.. code-block:: java

    package com.revs.designpatterns.singleton;

    class LazySingleton {
        private static LazySingleton instance;

        private LazySingleton() {
            System.out.println("initializing Singleton");
        }

    //    public static LazySingleton getInstance() {
    //        if(instance == null) {
    //            instance = new LazySingleton();
    //        }
    //        return instance;
    //    }

        // threadsafety
    //    public static synchronized LazySingleton getInstance() {
    //        if(instance == null) {
    //            instance = new LazySingleton();
    //        }
    //        return instance;
    //    }

        // double-checked locking
        public static LazySingleton getInstance() {
            if (instance == null) {
                synchronized (LazySingleton.class) {
                    if (instance == null) {
                        instance = new LazySingleton();
                    }
                }
            }
            return instance;
        }
    }

    public class LazySingletonAndThreadSafetyDemo {

        public static void main(String[] args) {

        }
    }

Innetr Static Class Singleton
-----------------------------

.. code-block:: java


