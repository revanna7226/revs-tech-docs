Coding Challenge Questions
==========================

1. How do you reverse a string in Java?

There is no reverse() utility method in the String class. However, you can create a character array from the string and then iterate it from the end to the start. You can append the characters to a string builder and finally return the reversed string.

.. code-block:: java

    public class Main {
        public static void main(String[] args) {
            String strToReverse = "Revanna";
            reverseString(strToReverse);
        }

        private static void reverseString(String strValue) {
            if(strValue == null) {
                throw new IllegalArgumentException("Null is not a valid input!");
            }

            StringBuilder sb = new StringBuilder(strValue.length());
            char[] chars = strValue.toCharArray();

            for (int i = chars.length - 1 ; i >= 0 ; i--) {
                sb.append(chars[i]);
            }

            System.out.println(sb.toString());
            
        }
    }

    // Output:
    // annaveR


2. Swapping of two numbers without using third variable?

.. code-block:: java

    public class Main {
        public static void main(String[] args) {
            int a = 10;
            int b = 20;

            System.out.println("Before swapping: a = " + a + ", b = " + b);
            swap(a, b);
        }

        private static void swap(int a, int b) {
            a = a + b; // Step 1: Add both numbers
            b = a - b; // Step 2: Subtract the new value of 'a' with 'b' to get original 'a'
                       // a + b - b = a assigns b
            a = a - b; // Step 3: Subtract the new value of 'b' from 'a' to get original 'b'
                       // a + b - a = b -> a

            System.out.println("After swapping: a = " + a + ", b = " + b);
        }
    }
        b = a - b; 
        a = a - b; // a + b - a = b -> a
    // Output:
    // Before swapping: a = 10, b = 20
    // After swapping: a = 20, b = 10


3. Count the number of vowels in a string?

.. code-block:: java

    public class Main {
        public static void main(String[] args) {

            String name = "Revanna";

            Map<Character, Long> vowelCountMap = name.toLowerCase()
                    .chars()
                    .mapToObj(c -> (char) c)
                    .filter(c -> "aeiou".indexOf(c) != -1)
                    .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));

            System.out.println("Count of Each Vowels found: ");
            System.out.println(vowelCountMap);
            long vowelsCount = vowelCountMap.values().stream().mapToLong(Long::longValue).sum();
            System.out.println("Total Count : " + vowelsCount);

        }
    }

    // Output:
    // Count of Each Vowels found:  
    // {a=2, e=1}
    // Total Count : 3