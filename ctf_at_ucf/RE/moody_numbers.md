# Moody numbers

This challenge is for 100 points, and it comes with a .jar file. 

Using [the jadx decompiler](https://github.com/skylot/jadx), we are able to see the Java code. It turns out that this is a program where the user inputs a series of integers as passwords to get the flag.

```
package defpackage;

import java.nio.ByteBuffer;
import java.util.Scanner;
import javax.crypto.Cipher;
import javax.crypto.spec.SecretKeySpec;

/* renamed from: MoodyNumbers  reason: default package */
/* loaded from: MoodyNumbers.jar:MoodyNumbers.class */
class MoodyNumbers {
    MoodyNumbers() {
    }

    public static void main(String[] strArr) {
        Scanner scanner = new Scanner(System.in);
        NumberChecker numberChecker = new NumberChecker();
        System.out.println("Greetings, human! I am the Moody Number Bot.");
        sleep(1000);
        System.out.println("We're going to play a little game.");
        sleep(1000);
        System.out.println("Here's how it's going to go:");
        sleep(1000);
        System.out.println("I'm going to ask you to show me a number, and you're going to enter it in here.");
        sleep(1000);
        System.out.println("If you don't give me the right number, I'm going to get so angry that I stop talking to you.");
        sleep(1000);
        System.out.println("So don't give me the wrong numbers.");
        sleep(1000);
        System.out.println("Now that we've got that out of the way, let's begin!");
        sleep(1000);
        System.out.print("Show me a number that makes me happy: ");
        int nextInt = scanner.nextInt();
        if (!numberChecker.isHappy(nextInt)) {
            wrongNumber("THAT NUMBER DOES NOT MAKE ME HAPPY!!!");
        }
        System.out.println("Ah, that number fills me with joy! Good one!");
        sleep(1000);
        System.out.println("Okay, I have another request for you.");
        sleep(1000);
        System.out.print("I'm in the mood to be scared. Frighten me with a number: ");
        int nextInt2 = scanner.nextInt();
        if (!numberChecker.isScary(nextInt2)) {
            wrongNumber("IS THAT THE BEST YOU HAVE? THAT COULDN'T SCARE AN INFANT!!!");
        }
        System.out.println("AAAAAHHH!!! That was scary! I think I accidentally overflowed my buffer!");
        sleep(1000);
        System.out.print("Give me a number that reminds me of my childhood: ");
        int nextInt3 = scanner.nextInt();
        if (nextInt3 == 0) {
            wrongNumber("HOW DARE YOU INSULT MY CHILDHOOD!!!");
        } else if (!numberChecker.isNostalgic(nextInt3)) {
            wrongNumber("THIS NUMBER REMINDS ME OF THE TIME A MEAN HACKER ALMOST FRIED MY CIRCUITS, NOT MY CHILDHOOD!!!");
        }
        System.out.println("That number brings back memories of the time I received my first UDP packet!");
        sleep(1000);
        System.out.print("Now I want a number that arouses my circuits: ");
        int nextInt4 = scanner.nextInt();
        if (!numberChecker.isArousing(nextInt4)) {
            wrongNumber("THAT NUMBER IS SUCH A TURN-OFF THAT IT DISABLED MY NETWORK ADAPTER!!!");
        }
        System.out.println("Oooh, baby, that's a sexy number!");
        sleep(1000);
        System.out.println("Okay, you win. Here's your stupid flag. Goodbye.");
        sleep(1000);
        System.out.println(getFlag(nextInt, nextInt3, nextInt2, nextInt4));
        scanner.close();
    }

    static void sleep(int i) {
        try {
            Thread.sleep(i);
        } catch (InterruptedException e) {
        }
    }

    static void wrongNumber(String str) {
        System.out.println(str + " GET AWAY FROM ME!!!");
        System.exit(1);
    }

    static String getFlag(int i, int i2, int i3, int i4) {
        SecretKeySpec secretKeySpec = new SecretKeySpec(ByteBuffer.allocate(16).putInt(i).putInt(i2).putInt(i3).putInt(i4).array(), "AES");
        try {
            Cipher cipher = Cipher.getInstance("AES");
            cipher.init(2, secretKeySpec);
            return new String(cipher.doFinal(new byte[]{-70, 76, 66, -121, -86, -83, 121, 99, 64, 57, 38, 57, -126, 78, 41, -27, 81, 64, 106, 78, -85, 104, 2, -119, 57, 115, -48, 104, -110, 45, -12, 92, 89, -101, 49, 15, 22, 122, -71, -77, -8, 23, -102, 46, -31, 81, 60, -44}));
        } catch (Exception e) {
            System.out.println("An error occurred: " + e);
            return "ERROR";
        }
    }
}
```
`MoodyNumbers.java`

```
package defpackage;

import java.math.BigInteger;
import java.security.MessageDigest;

/* renamed from: NumberChecker */
/* loaded from: MoodyNumbers.jar:NumberChecker.class */
public class NumberChecker {
    public boolean isHappy(int i) {
        return i % 270719 == 0 && i / 270719 == 6317;
    }

    public boolean isScary(int i) {
        return (i & 255) == 0 && (i >> 12) == 0 && ((i >> 8) ^ 15) == 4;
    }

    public boolean isNostalgic(int i) {
        try {
            return String.format("%032x", new BigInteger(1, MessageDigest.getInstance("MD5").digest(Integer.toString(i).getBytes("UTF-8")))).equals("08ef85248841b7fbf4b1ef8d1090a0d4");
        } catch (Exception e) {
            System.out.println("An error occurred: " + e);
            return false;
        }
    }

    public boolean isArousing(int input) {
        int i = input % 10;
        int i2 = input / 10;
        int i3 = i2 % 10;
        int i4 = i2 / 10;
        if (i3 % 2 != 0 || i != (i3 / 2) * 3) {
            return false;
        }
        for (int j = 0; j < 3; j++) {
            if (i4 % 10 != i) {
                return false;
            }
            int i5 = i4 / 10;
            if (i5 % 10 != i3) {
                return false;
            }
            i4 = i5 / 10;
        }
        return i4 == 0 && i % 2 != 0 && (i ^ i3) == 15;
    }
}
```
`NumberChecker.java`

The first required number is 6317 multiplied by 270719.

The second number is 2816, since its last 8 bits and first 20 bits are 0, and the 4 remaining bits are 1, 0, 1 and 1 respectively, so the number is 256 + 512 + 2048 which gives 2816.

The third number can be found through a database of hashes such as [CrackStation](https://crackstation.net/), and it gives a value of 19800828. Alternatively, brute forcing is also enticing.

The last number seems to be quite difficult to be derived by hand, so I wrote some code (in C, for performance purposes) to call `isArousing` with all the positive integers until an input returns true. Such a number, which goes well with the name of this challenge, is 69696969.

# Flag:
`flag{th1s_1s_why_c0mpu73rs_d0n7_h4v3_f33l1ng5}`

