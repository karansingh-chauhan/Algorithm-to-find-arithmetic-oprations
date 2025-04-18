# Algorithm-to-find-arithmetic-oprations
package javamain;
import java.math.BigDecimal;
import java.math.MathContext;
import java.util.*;

public class Project {

    public static String trimLeadingZeros(String num) {
        return num.replaceFirst("^0+(?!$)", "");
    }

    // Add two large numbers
    public static String add(String num1, String num2) {
        StringBuilder result = new StringBuilder();

        int carry = 0;
        int i = num1.length() - 1, j = num2.length() - 1;

        while (i >= 0 || j >= 0 || carry > 0) {
            int digit1 = (i >= 0) ? num1.charAt(i--) - '0' : 0;
            int digit2 = (j >= 0) ? num2.charAt(j--) - '0' : 0;
            int sum = digit1 + digit2 + carry;

            carry = sum / 10;
            result.append(sum % 10);
        }

        return result.reverse().toString();
    }

    // Subtract two large numbers (num1 > num2)
    public static String subtract(String num1, String num2) {
        StringBuilder result = new StringBuilder();

        int borrow = 0;
        int i = num1.length() - 1, j = num2.length() - 1;

        while (i >= 0) {
            int digit1 = num1.charAt(i--) - '0';
            int digit2 = (j >= 0) ? num2.charAt(j--) - '0' : 0;
            digit1 -= borrow;

            if (digit1 < digit2) {
                digit1 += 10;
                borrow = 1;
            } else {
                borrow = 0;
            }

            result.append(digit1 - digit2);
        }

        return trimLeadingZeros(result.reverse().toString());
    }

    // Multiply two large numbers
    public static String multiply(String num1, String num2) {
        int len1 = num1.length();
        int len2 = num2.length();
        int[] result = new int[len1 + len2];

        for (int i = len1 - 1; i >= 0; i--) {
            for (int j = len2 - 1; j >= 0; j--) {
                int mul = (num1.charAt(i) - '0') * (num2.charAt(j) - '0');
                int sum = mul + result[i + j + 1];

                result[i + j + 1] = sum % 10;
                result[i + j] += sum / 10;
            }
        }

        StringBuilder sb = new StringBuilder();
        for (int num : result) {
            if (!(sb.length() == 0 && num == 0)) {
                sb.append(num);
            }
        }

        return sb.length() == 0 ? "0" : sb.toString();
    }

    // Divide two large numbers using Newton-Raphson method with error handling
    public static String divide(String dividendStr, String divisorStr, int precision) {
        // Check for division by zero
        if (divisorStr.equals("0") || divisorStr.matches("^0+(\\.0+)?$")) {
            return "Error: Division by zero is undefined.";
        }

        try {
            BigDecimal A = new BigDecimal(dividendStr);
            BigDecimal B = new BigDecimal(divisorStr);
            MathContext mc = new MathContext(precision);

            // Initial approximation of 1 / B
            BigDecimal x = BigDecimal.ONE.divide(B, mc);

            // Newton-Raphson Iteration: x = x * (2 - B * x)
            for (int i = 0; i < precision; i++) {
                x = x.multiply(BigDecimal.valueOf(2).subtract(B.multiply(x, mc), mc), mc);
            }

            // Result: A * (1 / B)
            BigDecimal result = A.multiply(x, mc);

            return result.stripTrailingZeros().toPlainString();
        } catch (ArithmeticException e) {
            return "Error during division: " + e.getMessage();
        } catch (NumberFormatException e) {
            return "Error: Invalid number format.";
        }
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("Enter first large number:");
        String num1 = scanner.nextLine();
        System.out.println("Enter second large number:");
        String num2 = scanner.nextLine();

        System.out.println("Addition: " + add(num1, num2));
        System.out.println("Subtraction: " + subtract(num1, num2));
        System.out.println("Multiplication: " + multiply(num1, num2));
        System.out.println("Division (Newton-Raphson): " + divide(num1, num2, 50));
    }
}
