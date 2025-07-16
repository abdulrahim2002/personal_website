---
date: '2025-07-16T16:06:55+05:30'
math: true
author: abdul
title: 'Leetcode_564_find_the_closest_palindrome'
---

We are given a number `n`. On the number line, there are infinite numbers on the right of `n` and left of `n` that are palindromes. 

The question is asking us to find a number that is a palindrome and has the minimum distance from `n` on the number line.

![ok](https://i.ibb.co/4ZrF3Tnx/Screenshot-from-2025-06-28-18-27-24.png)

We break the problem into 2 parts:
- find the next element on the number line after `n` that is a palindrome.
- find the previous element on the number line before `n` that is a palindrome.
-  Suppose `i` and `j` are numbers such that `i < n < j` and `i` and `j` are both palindromes. And `i` and `j` are the nearest numbers to `n` on the number line
- We just find which of `i` and `j` is the nearer one to `n` on the number line and return it

The simplest way of doing this is:

    i = n-1
    j = n+1

    while ( !isPalindrome(i) ) i--;
    while ( !isPalindrome(j) ) j++;

    dist_j = j - n
    dist_i = n - i

    if ( dist_i == dist_j ) return i
    return ( dist_i < dist_j ) ? i : j
    
However, given that `n` could be as high as $$10^{18}$$. This approach is impractical.

Luckly, there is an efficient approach to find the next and previous numbers to a particular number that is a plindrome.

Consider the number `123`. Lets try to find out the next larger palindrome to `123`

We can break this number into 2 parts: `12` and `3`. Note that we incude the left value in left half when the length is odd.

- We can construct a palindrome by mirroring the first half as:`121`.
- However, `121 < 123` therefore we need a larger algorithm. We do this by simply increasing the left half to `12 + 1 = 13`.
- The palindrome can then be constructed as `131` which is larger than `123`. You shall keep on increasing the left half, until the palindrome you obtain becomes larger enough.
- If you observe carefully, then you will notice that `131` is the smallest number on the number line that is a palindrome to the right of `123`. This holds true in general.

There is an edge case where this appraoch does not work. Particularly when n = '999999999......' The next palindrome in this case is given by `n+2`. For example, next palindrome of `999=1001, 99999=100001`  and so on.

The previous palindrome to `n` can also be generated this way. Insted of increasing the left half, we would decrease it instead. Again, a note of caution, there are 2 edge cases. When n = `1000.....0001` then the previous palindrome is given by n-2. For example, previous palindrome of `101=99, 1001=999` and so on.

Moreover, the previous palindrome of `1000...00` is given by n-1. For example previous palindrome of `100=99, 1000=999` and so on.

---

```python3 []
class Solution:
    def nearestPalindromic(self, n: str) -> str:
        next_pal = self.find_next_pal( n )
        next_diff = int(next_pal) - int(n)
        prev_pal = self.find_prev_pal( n )
        prev_diff = int(n) - int(prev_pal)

        if ( next_diff == prev_diff ): 
            return prev_pal
        
        return next_pal if next_diff < prev_diff else prev_pal
    
    def find_next_pal( self, num: str ) -> str:
        # edge case: num like 9, 99, 99999, 9999999 etc.
        if re.match( r'^9+$', num ):
            return str( int(num) + 2 )
        
        # step 1: split the number into left and right half
        mid = (1+len(num))//2
        left_half = num[:mid]

        while True:
            # step 2: find current palindrome by mirroring left half
            if len(num) % 2 == 0:
                pal = left_half + left_half[::-1]
            else:
                pal = left_half + left_half[:-1][::-1]
            
            # step 3: increment left half if palindrome is not greater than num
            if int(pal) <= int(num):
                left_half = str( int(left_half) + 1 )
            else:
                return pal

    def find_prev_pal( self, num: str ) -> str:
        # edge case: num like 101, 1001, 1000001 or 10 10000 1000000 etc.
        if re.match( r'^10*1$', num ) or re.match( r'^10+$', num ):
            return str(int(num)-2) if re.match( r'^10*1$', num ) else str(int(num)-1)
        
        # step 1: split the number into left and right half
        mid = len(num)//2
        left_half = num[:mid] if len(num)%2==0 else num[:mid+1]

        while True:
            # step 2: find current pal by mirroring left half
            if len(num) % 2 == 0:
                pal = left_half + left_half[::-1]
            else:
                pal = left_half + left_half[:-1][::-1]
            
            # step 3: decrement left half if palindrome is not smaller than num
            if int(pal) >= int(num):
                left_half = str( int(left_half) - 1 )
            else:
                return pal
```

``` java []
import java.util.regex.Pattern;
import java.math.BigInteger;

class Solution {
    public String nearestPalindromic(String n) {
        String nextPal = nextPalindrome(n);
        String prevPal = prevPalindrome(n);

        BigInteger diffNext = new BigInteger(nextPal).subtract( new BigInteger(n) );
        BigInteger diffPrev = new BigInteger(n).subtract(new BigInteger(prevPal));

        // If differences are equal, return the smaller one
        if (diffNext.compareTo(diffPrev) == 0) {
            return prevPal;
        }

        // Return the one with smaller difference
        return diffNext.compareTo(diffPrev) < 0 ? nextPal : prevPal;
    }

    public static String nextPalindrome(String num) {
        // Edge case: num is all 9s (e.g., "9", "99", "999999")
        if (Pattern.matches("^9+$", num)) {
            return new BigInteger(num).add(BigInteger.TWO).toString();
        }

        int n = num.length();
        int mid = n / 2;
        String leftHalf = n % 2 == 0 ? num.substring(0, mid) : num.substring(0, mid + 1);

        while (true) {
            // Mirror left half to form the palindrome
            String palindrome;
            if (n % 2 == 0) {
                palindrome = leftHalf + new StringBuilder(leftHalf).reverse().toString();
            } else {
                palindrome = leftHalf + new StringBuilder(
                                                leftHalf.substring(0, leftHalf.length() - 1)
                                            ).reverse().toString();
            }

            // increment left half
            if (new BigInteger(palindrome).compareTo(new BigInteger(num)) <= 0) {
                leftHalf = new BigInteger(leftHalf).add(BigInteger.ONE).toString();
            }
            else return palindrome;
        }
    }

    public static String prevPalindrome(String num) {
        // Edge case: num like "1001", "100001", "10000000001" etc.
        if (Pattern.matches("^10*1$", num)) {
            return new BigInteger(num).subtract(BigInteger.TWO).toString();
        }

        // Edge case: num like "100", "10000", "10000000" etc.
        if (Pattern.matches("^10*$", num)) {
            return new BigInteger(num).subtract(BigInteger.ONE).toString();
        }

        int n = num.length();
        int mid = n / 2;
        String leftHalf = n % 2 == 0 ? num.substring(0, mid) : num.substring(0, mid + 1);

        while (true) {
            // Mirror left half to form the palindrome
            String palindrome;
            if (n % 2 == 0) {
                palindrome = leftHalf + new StringBuilder(leftHalf).reverse().toString();
            } else {
                palindrome = leftHalf + new StringBuilder(
                                                leftHalf.substring(0, leftHalf.length() - 1)
                                            ).reverse().toString();
            }

            // devrement left half is palindromw is larger
            if (new BigInteger(palindrome).compareTo(new BigInteger(num)) >= 0) {
                leftHalf = new BigInteger(leftHalf).subtract(BigInteger.ONE).toString();
            }
            else return palindrome;
        }
    }

}
```

```typescript []
function nearestPalindromic(n: string): string {
    const   next = nextPalindrome(n),
            prev = prevPalindrome(n),
            prevDiff = BigInt( n ) - BigInt( prev ),
            nextDiff = BigInt( next ) - BigInt( n );

    if ( prevDiff === nextDiff ) return prev;
    
    return ( prevDiff < nextDiff ) ? prev : next;
}

function nextPalindrome( num: string ): string {
    // edge case: num = 9*
    if ( /^9+$/.test(num) ) return String( BigInt(num) + 2n );

    // step 1: split the number into left and right half
    const   N = num.length,
            mid = Math.floor( N/2 );
    
    let leftHalf = (N%2==0) ? num.slice(0, mid) : num.slice( 0, mid+1 );

    while ( true ) {
        // step 2: form the current palindrome by mirroring left half
        let curPalindrome;
        if ( N%2==0 ) {
            curPalindrome = leftHalf + leftHalf.split('').reverse().join('');
        }
        else {
            curPalindrome = leftHalf + leftHalf.slice(0,-1).split('').reverse().join('');
        }
        
        // step 3: increment left half if current palindrome is not greater then num
        if ( BigInt(curPalindrome) <= BigInt(num) ) {
            leftHalf = String( BigInt(leftHalf) + 1n );
        }
        else return curPalindrome;
    }
}

function prevPalindrome( num: string ): string {
    // edge case: num = 1(0*)1
    if ( /^10*1$/.test(num) ) return String( BigInt(num) - 2n );
    if ( /^10+$/.test(num) )  return String( BigInt(num) - 1n );

    // step 1: split the number into left and right half
    const   N = num.length,
            mid = Math.floor( N/2 );
    
    let leftHalf = (N%2==0) ? num.slice(0, mid) : num.slice( 0, mid+1 );

    while ( true ) {
        // step 2: form the current palindrome by mirroring left half
        let curPalindrome;
        if ( N%2==0 ) {
            curPalindrome = leftHalf + leftHalf.split('').reverse().join('');
        }
        else {
            curPalindrome = leftHalf + leftHalf.slice(0,-1).split('').reverse().join('');
        }
        
        // step 3: decrement left half if current palindrome is not smaller than num
        if ( BigInt(curPalindrome) >= BigInt(num) ) {
            leftHalf = String( BigInt(leftHalf) - 1n );
        }
        else return curPalindrome;
    }
}
```

``` javascript []
/**
 * @param {string} n
 * @return {string}
 */
var nearestPalindromic = function(n) {
    const next = nextPalindrome(n),
          prev = prevPalindrome(n),
          prevDiff = BigInt(n) - BigInt(prev),
          nextDiff = BigInt(next) - BigInt(n);

    if (prevDiff === nextDiff) return prev;

    return (prevDiff < nextDiff) ? prev : next;
};

function nextPalindrome(num) {
    // edge case: num = 9*
    if (/^9+$/.test(num)) return String(BigInt(num) + 2n);

    // step 1: split the number into left and right half
    const N = num.length,
          mid = Math.floor(N / 2);

    let leftHalf = (N % 2 === 0) ? num.slice(0, mid) : num.slice(0, mid + 1);

    while (true) {
        // step 2: form the current palindrome by mirroring left half
        let curPalindrome;
        if (N % 2 === 0) {
            curPalindrome = leftHalf + leftHalf.split('').reverse().join('');
        } else {
            curPalindrome = leftHalf + leftHalf.slice(0, -1).split('').reverse().join('');
        }

        // step 3: increment left half if current palindrome is not greater than num
        if (BigInt(curPalindrome) <= BigInt(num)) {
            leftHalf = String(BigInt(leftHalf) + 1n);
        } else {
            return curPalindrome;
        }
    }
}

function prevPalindrome(num) {
    // edge case: num = 1(0*)1 or 1(0*)
    if (/^10*1$/.test(num)) return String(BigInt(num) - 2n);
    if (/^10+$/.test(num)) return String(BigInt(num) - 1n);

    // step 1: split the number into left and right half
    const N = num.length,
          mid = Math.floor(N / 2);

    let leftHalf = (N % 2 === 0) ? num.slice(0, mid) : num.slice(0, mid + 1);

    while (true) {
        // step 2: form the current palindrome by mirroring left half
        let curPalindrome;
        if (N % 2 === 0) {
            curPalindrome = leftHalf + leftHalf.split('').reverse().join('');
        } else {
            curPalindrome = leftHalf + leftHalf.slice(0, -1).split('').reverse().join('');
        }

        // step 3: decrement left half if current palindrome is not smaller than num
        if (BigInt(curPalindrome) >= BigInt(num)) {
            leftHalf = String(BigInt(leftHalf) - 1n);
        } else {
            return curPalindrome;
        }
    }
}

```

