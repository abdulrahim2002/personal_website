---
date: '2025-07-16T00:41:07+05:30'
draft: false
title: 'Leetcode 70'
author: abdul
math: true
---

Problem link: [leetcode 70: climbing stairs](https://leetcode.com/problems/climbing-stairs/description/)

# Intuition
<!-- Describe your first thoughts on how to solve this problem. -->
You need to climb `n` stairs, taking 1 or 2 stairs at a time.

If n = 2, you can climb like: 
- $$1+1 = 2\times (1) + 0 \times(2)$$
- $$2 = 0 \times (1) + 1\times(2) $$

if n = 3, you can climb like:
- $$1+1+1 =  3\times(1) + 0\times(2)$$
- $$1+2   =  1\times(1) + 1\times(2)$$
- $$2+1   =  1\times(1) + 1\times(2) $$

Basically you first need to decide how many steps of size 1 will you take and how many of size 2 do you need:

Hence, your first task is to solve the equation:


$$
x \times 1 + y \times 2 = n 
$$ 

To decide the number of 1s and 2s. After you decide upon x and y  then you will have calculate
$$ 
\frac{(x + y)!}{x! \, y!}
$$

Which is nothing but ways of chosing how exactly you will proceed. This is because we are trying to adjust x identical objects and y identical objects in x+y positions. Think of the number of ways you can arrange x men and y women in x + y positions.

As an example $$n=3, x=1 $$ and $$y=1$$ 

Then you will have $$\frac{(1 + 1)!}{1! \, 1!} =2$$. see above, they are:
$$
1+2 \\
2+1
$$


# Approach
<!-- Describe your approach to solving the problem. -->
 $$ y \in [0, n/2] $$

For each , calculate the corresponding values of $$x$$ using the equation:
$$
x \times 1 + y \times 2 = n \\
x = n - (2 \times y )
$$ 

then calculate 
$$ 
\frac{(x + y)!}{x! \, y!}
$$

and add this to your counter variable.

return counter.

# Complexity
- Time complexity:
<!-- Add your time complexity here, e.g. $$O(n)$$ -->
Time: O($$n^2$$)  
Space: O(1)

But it can be reduced, if you can store calculated factorials. Hence, making the time complexity of calculating $$ 
\frac{(x + y)!}{x! \, y!}
$$ -> O(1) and Space complexity O(n)

Time complexity: O(n)
Space complexity: O(n)

# Code
```javascript []
var permute = (x,y) => {
    /* compute (x+y)! / x! y! without computing factorials */
    /* which is basically computing x+y x+y-1 x+y-2 .. y+1 / x! */
    var denom = 1;
    for (var i=1; i <= x; i++) denom *= i;

    var numi = 1;
    for (var i = y+1; i <= x+y; i++) numi *= i;

    return numi/denom;
};



var climbStairs = function(n) {
    /* The number of ways in which we can get n by adding only 1 and 2
       Let,
       1x + 2y = n ---(1)
       then we need to find the number of integer solutions to this equation.
       that is S = {x,y | x,y in integers}  we need to return |S| i.e. the
       number of elements in this solution set.
       from (1) it implies x = n - 2y ---(2)
       y in range [0,n/2] i.e. consequently x in range [0,n]
       hence, iterate over y from 0 to n/2 such that y is integer.
       we find the corrosponding solution using equation 2
    */
    let n_solutions = 0 ;

    for (var y=0; y <= Math.floor(n/2); y++ ) {
        var x = n - (2 * y);
        n_solutions += permute(x,y);
    }

    return  (n_solutions);
};

```
