# ticker
ticks per second

````java
package eu.byncing.test;

public class Ticker {

    public static final int DELAY = 1;

    public static final int TICKS_PER_SECONDS = 20;
    public static final int TICKS = 1000;

    public static final int FLOOR_TICKS_PER_SECONDS = TICKS_PER_SECONDS / 4;

    private long ticks, current, tick;

    private double ticksPerSeconds = TICKS_PER_SECONDS, delay;

    private int lost;

    public static boolean sleep(long duration) {
        try {
            Thread.sleep(duration);
            return true;
        } catch (InterruptedException e) {
            return false;
        }
    }

    public void start(Runnable runnable) {
        current = System.currentTimeMillis();
        tick = System.currentTimeMillis();

        double ticksPerSeconds = 0, delay = 0;

        while (true) {
            long millis = System.currentTimeMillis();
            if ((System.currentTimeMillis() - tick) > (TICKS / TICKS_PER_SECONDS)) {
                tick += (TICKS / TICKS_PER_SECONDS);
                runnable.run();

                ticksPerSeconds++;

                if (ticksPerSeconds > TICKS_PER_SECONDS) {
                    long duration1 = (long) ((ticksPerSeconds) - delay);
                    sleep(duration1);

                    //System.out.println("out: " + ticksPerSeconds);

                    ticksPerSeconds = TICKS_PER_SECONDS;
                }

                double v = (double) (System.currentTimeMillis() - millis) / 1000;
                if (delay > v) delay = v;
            }

            boolean sleep = sleep(DELAY);
            if (!sleep) break;

            if ((System.currentTimeMillis() - current) > 1000) {
                current += 1000;
                ticks++;

                this.ticksPerSeconds = ((ticksPerSeconds) - delay);
                this.delay = delay;
                this.lost = (int) ((TICKS_PER_SECONDS - this.ticksPerSeconds));

                if (ticks > 4) {
                    ticks = 0;
                    if (ticksPerSeconds < FLOOR_TICKS_PER_SECONDS) {
                        System.out.println("Warning: The ticker runs below the minimum!");
                    }
                    System.out.println("Info: (Ticks per seconds: " + this.ticksPerSeconds + "), (Delay: " + delay + "), (Lost: " + lost + ")");
                }

                delay = 0;
                ticksPerSeconds = 0;
            }

        }
    }

    public long getCurrent() {
        return current;
    }

    public long getTick() {
        return tick;
    }

    public double getTicksPerSeconds() {
        return (ticksPerSeconds - delay) / 1000;
    }

    public double getDelay() {
        return delay;
    }

    public int getLost() {
        return lost;
    }
}
````
