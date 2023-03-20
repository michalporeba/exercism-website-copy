### Problem
To solve the challenge we need to be able to check if a number is a palindrome (if it reads the same 
from left to right and from right to left). There are multiple ways to achieve this and we will look into 
the details later, but for now let's assume we have `bool IsPalindrome(int number)` function that can check a number.

The second element of the challenge is that we are looking for palindrome product, so for pairs of numbers from a given range
which when multiplied result in a palindrome number. 

### Approaches

**Approach 1:** The most common approach in the community solutions is to start with generating the pairs of numbers and checking 
if their product is a palindrome.

Here is an example solution:
```

private static IEnumerable<(int, int)> AscendingNumberPairs(int min, int max) {
  for (var i = min; i <= max; ++i) {
    for (var j = i; j <= max; ++j) {
      yield return (i, j);
    }
  }
}
    
public static (int, IEnumerable<(int,int)>) Smallest(int min, int max)
{
  ValidateInput(min, max);
  var smallestProduct = int.MaxValue;
  var factors = new List<(int, int)>();
  
  foreach(var (a, b) in AscendingNumberPairs(min, max)) {
    if (a > smallestProduct) { break; }
    var product = a * b;
    if (!IsPalindrome(product) || product > smallestProduct) { continue; }
    
    if (product < value) { 
        smallestProduct = product;
        factors.Clear();
    }
    
    factors.Add((a, b));
  }
  
  if (results.Count == 0) { 
    throw new ArgumentException("No palindromes in the range");
  }
  
  return (value, results.ToArray());
}
```

The above approach might be the most intuitive, but doens't perform well. Even with some optimisations it still performs poorly. 

**Approach 2:** The alternative, although perhaps less intuitive approach, is to start with finding the smallest / largest palindrome number first
and then discover its integer factors within the given range. For now, let's imagine that there is an extension method on an `int` type 
that can do it for us, something like `List<(int, int)> DivisorsInRange(this int number, int min, int max)`

```
public static (int, IEnumerable<(int,int)>) Smallest(int min, int max)
{
  ValidateInput(min, max);
  
  var palindrome = 0;
  var factors = new List<(int, int)>();
  
  for(int i = min * min; i <= max * max; ++i) {
    if (IsPalindrome(i)) {
      factors = i.DivisorsInRange(min, max);
      if (factors.Count > 0) {
        palindrome = i;
        break;
      }
    }
  }
  
  if (palindrome == 0) { 
    throw new ArgumentException();
  }
  
  return (palindrome, pairs.ToArray());
}
```

The above solution performs much better, but reads similarly. 

**Approach 3:** We can further simplif this code by using 
[Language Integrated Query (LINQ)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/). 

```
using System.Linq;
using Factors = System.Collections.Generic.IEnumerable<(int, int)>;

public static (int, Factors) Smallest(int min, int max)
{
  ValidateInput(min, max);

  var factors = Enumerable
    .Range(min * min, max * max)
    .Where(IsPalindrome)
    .Select(x => x.DivisorsInRange(min, max))
    .FirstOrDefault(x => x.Count > 0);

  if (pairs == null) { 
    throw new ArgumentException();
  }
  
  return (factors[0].Item1 * factors[0].Item2, factors.ToArray());
}
```

### IsPalindrome approaches

**Appraoch 1:** Convert number to a string, reverse it and compare it to the original.

```
private static bool IsPalindromeString(int number)
  =>  number.ToString().Equals(new String(number.ToString().Reverse().ToArray()));
```

**Approach 2:** Use modulo division to get values on individual positions and a stack to reverse it.
```
private static bool IsPalindromeStack(int number) { 
  var stack = new Stack<int>();
  var n = number;

  while (n > 0) { 
    stack.Push(n % 10);
    n /= 10;
  }

  var position = 0;
  var reversed = 0;
  while (stack.Count > 0) { 
    reversed += stack.Pop() * (int)Math.Pow(10, position++);
  }
  
  return number == reversed;
}
```

**Approach 3:** use logarithm to calculate value of the highest position
```
private static bool IsPalindromeMath(int number) {         
  var position = (int)Math.Log10(number);
  var reversed = 0;
  var n = number;

  while (n > 0) { 
    reversed += (n % 10) * (int)Math.Pow(10, position--);
    n /= 10;
  }

  return number == reversed;
}
```

*(Performance differences should be discussed and illustrated here).*
