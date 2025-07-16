---
date: '2025-07-16T00:37:54+05:30'
draft: false
title: 'Leetcode 135: Candy'
math: true
author: abdul
---

Below is my C solution for the Leetcode problem [candy](https://leetcode.com/problems/climbing-stairs/description/).


```c []
static inline void previous_kids_started_crying(int* kid,
                                  int continous_decrease_start,
                                  int continous_decrease_end, int* candy)
{
        /*
           * To satisfy the condition that each child with higher rating than
           * it's neighbors must get more candies. We give all children in range
           * [continous_decrease_start, continous_decrease_end] an extra candy
           * if the he has candies lower or equal to the kid after it.
           *
           * Note that we do not bother the kid before it, since it is known
           * that the kid before it has more rating than this kid
        */
	if (continous_decrease_start == -1) {
		exit(1);
	}
        for (int i = continous_decrease_end; i >= continous_decrease_start; i--) {

                if (kid[i] <= kid[i+1]) {
                        int before = kid[i];
                        kid[i]++;
                        int after = kid[i];
                        *candy += after - before;;
                }
                else {
                        /* chain is broken. We found a satisfied non protesting kid
                          the kids before it must also be non-protesting */
                        return;
                }
        }
}

int candy(int* kid, int n)
{
        /*
           * Few things to take care of:
           *
           * 1. At a time take a look at 2 children. i.e. iterate the children
           * in window of 2.
           *
           * 2. Each time a candy is given, check if previous child has more
           * rating, since he will start crying. He/she will protest that the
           * kid in front of him has less rating than him/her and still got
           * more/equal candies than him/her. It might even trigger a chain
           * where the kid previous to the previous kid might also see this
           * changed state, and if he had a rating more then the kid after him.
           * then he will also start asking more candies. And the kid behind
           * that and so on.
           *
           * The algorithm runs in linear time, however, if the children are
           * arranged in decreasing order of rating, then at each iteration all
           * previous children will start crying. Hence, everybofy needs to be
           * given more candies. In that case it becomes quadratic
           *
           * Space required is constant.
           *
           * Best case:
           * Time: O(n)
           * Space: O(1)
           *
           * Worst Case:
           * Time: O(n^2)
           * Space: O(1)
         */

        int candies = 0;
        int continous_decrease_from = -1;

        /* First child gets a candy, but save his rating first */
        int previous_child_rating = kid[0];
        kid[0] = 1;
        candies++;

        for (int i = 1; i < n; i++) {
                if (kid[i] > previous_child_rating) {
                        /* Since this child has more rating than the previous
                         child, this child gets 1 more candy than previous
                         child */
                        previous_child_rating = kid[i];
                        kid[i] = kid[i-1]+1;
                        candies += kid[i];
                        continous_decrease_from = -1;
                }
                else if (kid[i] == previous_child_rating){
                        /* Since this child has equal rating than the
                         previous child, give him 1 candy */
                        previous_child_rating = kid[i];
                        kid[i] = 1;
                        candies++;
                        continous_decrease_from = -1;
                }
                else {
                        /* Previous child has more rating. Give this child one
                        candy, and after giving : if the previous child had less
                        candy, then we must initiate a chain reaction to do
                        justice to all previous kids to previous children

                        the problem here is that if this child has less rating
                        then his predecessor then he will also start crying.
                        And, the predecesor of this child also has more score
                        then he will also start crying. Hence, we must give
                        candies to them as well to maintain rule. Hence, check
                        if the rule is disturbed,
                        */
                        if (continous_decrease_from == -1) {
                                /* A period of continous decrease started */
                                /* Hence, give this child 1 candy and if the
                                   previous child will also get 1 candy if he gets  */
                                continous_decrease_from = i-1;
                                previous_child_rating = kid[i];
				kid[i] = 1;
				if (kid[i-1] > kid[i]) {
					candies ++;
                                }
				else {
					kid[i-1] = kid[i] + 1;
					candies += 2;
				}
                        }
                        else {
                                /* Give this child a candy, but mind the children
                                  behind this child */
                                previous_child_rating = kid[i];
                                kid[i] = 1;
                                candies++;
                                previous_kids_started_crying(kid,
                                                             continous_decrease_from,
                                                             i-1,
                                                             &candies);
                        }
                }
        }
        return candies;
}

```

