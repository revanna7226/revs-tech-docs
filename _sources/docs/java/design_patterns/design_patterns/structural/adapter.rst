
When to use
-----------

- You want to reuse an existing class but its interface does not match the
  one you need.
- You want to create a reusable class that cooperates with unrelated or
  unforeseen classes.
- To provide a thin wrapper that converts data or calls between modules.

Example 1 — Object Adapter (composition)
----------------------------------------

Scenario: A simple media player application expects to use the `MediaPlayer`
interface (`play(String audioType, String fileName)`). We have legacy classes
that can play specific formats (`VlcPlayer`, `Mp4Player`) with different
methods. We create a `MediaAdapter` that adapts those classes to the
`MediaPlayer` interface.

.. code-block:: java

    // Target interface expected by client
    public interface MediaPlayer {
        void play(String audioType, String fileName);
    }

.. code-block:: java

    // Adaptee interface (legacy or third-party)
    public interface AdvancedMediaPlayer {
        void playVlc(String fileName);
        void playMp4(String fileName);
    }

.. code-block:: java

    // Concrete adaptees
    public class VlcPlayer implements AdvancedMediaPlayer {
        @Override
        public void playVlc(String fileName) {
            System.out.println("Playing vlc file. Name: " + fileName);
        }

        @Override
        public void playMp4(String fileName) {
            // do nothing
        }
    }

.. code-block:: java

    public class Mp4Player implements AdvancedMediaPlayer {
        @Override
        public void playVlc(String fileName) {
            // do nothing
        }

        @Override
        public void playMp4(String fileName) {
            System.out.println("Playing mp4 file. Name: " + fileName);
        }
    }

.. code-block:: java

    // Object Adapter: implements the Target and uses an Adaptee
    public class MediaAdapter implements MediaPlayer {

        private AdvancedMediaPlayer advancedMusicPlayer;

        public MediaAdapter(String audioType) {
            if (audioType.equalsIgnoreCase("vlc")) {
                advancedMusicPlayer = new VlcPlayer();
            } else if (audioType.equalsIgnoreCase("mp4")) {
                advancedMusicPlayer = new Mp4Player();
            }
        }

        @Override
        public void play(String audioType, String fileName) {
            if (audioType.equalsIgnoreCase("vlc")) {
                advancedMusicPlayer.playVlc(fileName);
            } else if (audioType.equalsIgnoreCase("mp4")) {
                advancedMusicPlayer.playMp4(fileName);
            }
        }
    }

.. code-block:: java

    // Concrete Target implementation that can play mp3 directly and delegates
    // other formats to the adapter
    public class AudioPlayer implements MediaPlayer {

        private MediaAdapter mediaAdapter;

        @Override
        public void play(String audioType, String fileName) {
            if (audioType.equalsIgnoreCase("mp3")) {
                System.out.println("Playing mp3 file. Name: " + fileName);
            } else if (audioType.equalsIgnoreCase("vlc") || audioType.equalsIgnoreCase("mp4")) {
                mediaAdapter = new MediaAdapter(audioType);
                mediaAdapter.play(audioType, fileName);
            } else {
                System.out.println("Invalid media. " + audioType + " format not supported");
            }
        }
    }

.. code-block:: java

    // Client
    public class ObjectAdapterDemo {
        public static void main(String[] args) {
            MediaPlayer player = new AudioPlayer();

            player.play("mp3", "beyond_the_horizon.mp3");
            player.play("mp4", "alone.mp4");
            player.play("vlc", "far_far_away.vlc");
            player.play("avi", "mind_me.avi");
        }
    }

Expected output:

.. code-block:: text

    Playing mp3 file. Name: beyond_the_horizon.mp3
    Playing mp4 file. Name: alone.mp4
    Playing vlc file. Name: far_far_away.vlc
    Invalid media. avi format not supported

Notes
-----
- The **MediaAdapter** composes the `AdvancedMediaPlayer` adapters (Vlc/Mp4)
  and converts the `play(audioType, fileName)` call into the proper method on
  the adaptee.
- This approach is flexible — new formats can be added by adding new
  `AdvancedMediaPlayer` implementations and updating the adapter.

Example 2 — Class Adapter (inheritance)
---------------------------------------

**Note:** Java allows class adapter implementation by extending the Adaptee class
and implementing the Target interface. This works when the Adaptee is a class
you can extend (not final) and you can use inheritance.

Scenario: You have a legacy `LegacyRectangle` with `draw(x1,y1,x2,y2)` that
draws rectangles with two corner points. Your new system expects `Shape`
interface with `draw(int x, int y, int width, int height)`.

.. code-block:: java

    // Target expected by the client
    public interface Shape {
        void draw(int x, int y, int width, int height);
    }

.. code-block:: java

    // Adaptee: legacy API
    public class LegacyRectangle {
        public void draw(int x1, int y1, int x2, int y2) {
            System.out.println("LegacyRectangle: (" + x1 + "," + y1 + ") to (" + x2 + "," + y2 + ")");
        }
    }

.. code-block:: java

    // Class Adapter: extends the adaptee and implements the target
    public class RectangleAdapter extends LegacyRectangle implements Shape {

        @Override
        public void draw(int x, int y, int width, int height) {
            // translate new parameters to legacy parameters
            int x2 = x + width;
            int y2 = y + height;
            // call adaptee method (inherited)
            draw(x, y, x2, y2);
        }
    }

.. code-block:: java

    // Client
    public class ClassAdapterDemo {
        public static void main(String[] args) {
            Shape rect = new RectangleAdapter();
            rect.draw(10, 20, 30, 40); // internally calls LegacyRectangle.draw(10,20,40,60)
        }
    }

Expected output:

.. code-block:: text

    LegacyRectangle: (10,20) to (40,60)

Notes
-----
- **Class Adapter** uses inheritance; you can override behaviour or forward calls.
- It is less flexible than object adapter if you need to adapt multiple adaptees
  or already extend another class (Java single inheritance limit).

Deep vs Shallow Adaptation
--------------------------
- Adapters may only **translate interfaces** (shallow) or may also **convert data
  formats** and add logic (deep). Keep adapters simple and focused on interface
  translation — complex transformations may indicate the need for a separate
  converter or mapper component.

Advantages / Disadvantages
--------------------------

.. list-table::
   :header-rows: 1
   :widths: 25 75

   * - **Advantages**
     - Allows reuse of existing classes without changing their source. Decouples
       client from concrete implementations. Supports two-way adaptation if needed.
   * - **Disadvantages**
     - Adds an extra layer (wrapper) which can increase complexity. Class adapter
       ties adapter to a specific Adaptee via inheritance and is less flexible.

Related Patterns
----------------
- **Facade:** provides a simpler interface to a set of interfaces; Adapter converts
  one interface to another. Facade simplifies a subsystem; Adapter makes two
  incompatible interfaces work together.
- **Decorator:** adds responsibilities to an object dynamically; Adapter changes
  interface. They can look similar but intent differs.
- **Proxy:** controls access to an object; Proxy and Adapter both wrap objects,
  but Proxy focuses on access control, Adapter on interface conversion.

Best Practices
--------------
- Prefer **Object Adapter (composition)** in Java for flexibility.
- Keep adapters **thin**; avoid putting business logic inside the adapter.
- If adapting third-party libraries, wrap them behind your own Target
  interfaces to simplify future changes.
- Use adapters to integrate legacy systems gradually without modifying existing code.

Summary
-------
- The Adapter pattern is a lightweight, widely-used structural pattern for
  integrating components with incompatible interfaces.
- Use object adapters (composition) in Java most of the time. Use class
  adapters when inheritance is appropriate and allowed.
- Adapters make code more modular, testable, and maintainable by isolating
  translation logic.

References
----------
- Also spelled *Adaptor* in some texts — same pattern, same intent.
- Typical uses: API integration, legacy code reuse, third-party library wrappers,
  platform-specific shims.

