import java.util.HashMap;

import java.util.Scanner;

class Main {
    private static final HashMap<Character, Integer> romanNumerals = new HashMap<>();
    
    static {
        romanNumerals.put('I', 1);
        romanNumerals.put('V', 5);
        romanNumerals.put('X', 10);
        romanNumerals.put('L', 50);
        romanNumerals.put('C', 100);
        romanNumerals.put('D', 500);
        romanNumerals.put('M', 1000);
    }

    private static boolean isNumeric(String str) {
        return str.matches("-?\\d+");
    }

    private static boolean isRoman(String str) {
        return str.matches("[IVXLCDM]+");
    }

    private static int romanToArabic(String roman) {
        int result = 0;
        int prevValue = 0;

        // Проход по римскому числу справа налево
        for (int i = roman.length() - 1; i >= 0; i--) {
            int value = romanNumerals.get(roman.charAt(i));
            // Если текущее значение меньше предыдущего, вычитаем его
            if (value < prevValue) {
                result -= value;
            } else {
                // Иначе прибавляем его к результату
                result += value;
            }
            prevValue = value;
        }

        return result;
    }

    public static String calc(String input) {
        String[] parts = input.split(" ");
        
        // Проверка корректности формата ввода
        if (parts.length != 3) {
            throw new IllegalArgumentException("Неправильный формат ввода");
        }

        String operand1 = parts[0];
        String operation = parts[1];
        String operand2 = parts[2];

        // Проверка, являются ли операнды числами
        if (!(isNumeric(operand1) || isRoman(operand1)) || !(isNumeric(operand2) || isRoman(operand2))) {
            throw new IllegalArgumentException("Операнды должны быть арабскими или римскими числами");
        }

        // Проверка поддержки операции
        if (!operation.matches("[+\\-*/]")) {
            throw new IllegalArgumentException("Неподдерживаемая арифметическая операция");
        }

        int num1, num2;
        // Преобразование операндов в числа
        if (isNumeric(operand1)) {
            num1 = Integer.parseInt(operand1);
            num2 = Integer.parseInt(operand2);
        } else {
            num1 = romanToArabic(operand1);
            num2 = romanToArabic(operand2);
        }

        // Проверка на диапазон операндов
        if ((num1 < 1 || num1 > 10) || (num2 < 1 || num2 > 10)) {
            throw new IllegalArgumentException("Операнды должны быть от 1 до 10");
        }

        int result;
        // Выполнение операции
        switch (operation) {
            case "+":
                result = num1 + num2; // Сложение
                break;
            case "-":
                result = num1 - num2; // Вычитание
                break;
            case "*":
                result = num1 * num2; // Умножение
                break;
            case "/":
                if (num2 == 0) {
                    throw new ArithmeticException("Деление на ноль");
                }
                result = num1 / num2; // Целочисленное деление
                break;
            default:
                throw new IllegalArgumentException("Неподдерживаемая арифметическая операция");
        }

        // Проверка на результат работы с римскими числами
        if (isRoman(operand1) && isRoman(operand2) && result <= 0) {
            throw new IllegalArgumentException("Результат работы с римскими числами должен быть положительным");
        }

        // Возврат результата
        return isNumeric(operand1) ? String.valueOf(result) : toRoman(result);
    }

    private static String toRoman(int number) {
        if (number < 1 || number > 3999) {
            throw new IllegalArgumentException("Римские числа поддерживаются от 1 до 3999");
        }

        StringBuilder roman = new StringBuilder();
        int[] values = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        String[] symbols = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};

        for (int i = 0; i < values.length; i++) {
            while (number >= values[i]) {
                number -= values[i];
                roman.append(symbols[i]);
            }
        }

        return roman.toString();
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("Введите выражение (например, 3 + 4) или римскими числами, используя верхний регистр латинских букв X,V,I:");
        String input = scanner.nextLine();
        scanner.close();

        try {
            System.out.println(calc(input));
        } catch (Exception e) {
            System.out.println("Ошибка: " + e.getMessage());
        }
    }
}


