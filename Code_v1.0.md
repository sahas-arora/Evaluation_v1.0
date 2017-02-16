# Evaluation_v1.0
/* A Java program to evaluate a given expression where tokens are separated
   by space.
   */
import java.util.Stack;

public class EvaluateExpr
{
    public static int evaluate(String expression)
    {
        char[] tokes = expression.toCharArray();

         // first Stack for numbers: 'values'
        Stack<Integer> values = new Stack<Integer>();

        // second Stack for Operators: 'opts'
        Stack<Character> opts = new Stack<Character>();

        for (int c = 0; c < tokes.length; c++)
        {
             // Skip if Current token is a blankspace
            if (tokes[c] == ' ')
                continue;

            // Current token is a number, push it to Values stack
            if (tokes[c] >= '0' && tokes[c] <= '9')
            {
                StringBuffer strbuf = new StringBuffer();
                // There may be multiple digits in the input number
                while (c < tokes.length && tokes[c] >= '0' && tokes[c] <= '9')
                    strbuf.append(tokes[c++]);
                values.push(Integer.parseInt(strbuf.toString()));
            }

            // Current token is an opening brace, push it to 'opts'
            else if (tokes[c] == '(')
                opts.push(tokes[c]);

            // Closing brace encountered, solve entire brace
            else if (tokes[c] == ')')
            {
                while (opts.peek() != '(')
                  values.push(applyOp(opts.pop(), values.pop(), values.pop()));
                opts.pop();
            }

            // Current token is an operator.
            else if (tokes[c] == '+' || tokes[c] == '-' ||
                     tokes[c] == '*' || tokes[c] == '/')
            {
                // While top of 'opts' has same or greater precedence to current
                // token, which is an operator. Apply operator on top of 'opts'
                // to top two elements in values stack
                while (!opts.empty() && hasPrecedence(tokes[c], opts.peek()))
                  values.push(applyOp(opts.pop(), values.pop(), values.pop()));

                // Push current token to 'opts'.
                opts.push(tokes[c]);
            }
        }

        // Entire expression has been parsed at this point, apply remaining
        // opts to remaining values
        while (!opts.empty())
            values.push(applyOp(opts.pop(), values.pop(), values.pop()));

        // Top of 'values' contains result, return it
        return values.pop();
    }

    // Returns true if 'op2' has higher or same precedence as 'op1',
    // otherwise returns false.
    public static boolean hasPrecedence(char op1, char op2)
    {
        if (op2 == '(' || op2 == ')')
            return false;
        if ((op1 == '*' || op1 == '/') && (op2 == '+' || op2 == '-'))
            return false;
        else
            return true;
    }

    // A utility method to apply an operator 'op' on operands 'a'
    // and 'b'. Return the result.
    public static int applyOp(char op, int x, int y)
    {
        switch (op)
        {
        case '+':
            return x + y;
        case '-':
            return x - y ;
        case '*':
            return x * y;
        case '/':
            if (y == 0)
                throw new
                UnsupportedOperationException("Exception: Cannot divide by zero");
            return x / y;
        }
        return 0;
    }

    // Driver method to test above methods
    public static void main(String[] args)
    {
          
       	System.out.println(EvaluateExpr.evaluate(" 14 * 4 + ( 222 / 37 * 2 ) - 68"));

    }
}
