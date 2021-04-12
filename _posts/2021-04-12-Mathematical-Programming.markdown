---
layout: post
title:  "Mathematical Programming"
date:   2021-04-12 13:58:42 +1000
categories: math CS
description: "Thoughts and examples in combining CS and math."
image: /assets/Sum.png
---
* W
{:toc}

After trying to understand some algorithms I have noticed some a connection between maths and the algorithm. A never really used this connection because I didn't really know how. I'm not in college yet so I'm still rather oblivious to the theory behind CS, especially the mathematical theory.

# Sum
A necessary tool for looking at algorithms are sums. Code which involves the use of a for loop can be described as a sum. Below are a few definitions for some sums.

## Arithmetic
A arithmetic sum is defined as follows:

$$s = 0 + 1 + 2 + ... + n$$

A more general way to express it looks like the following:

$$s = (n - 0) + (n - 1) + (n - 2) + ... + (n - n)$$

Notice we can separate the `n` and the numbers. One `n` is left since we "take" the `n` for the substitution from `(n - n)`.

$$s = n + n * n - 0 - 1 - 2 - ... - n$$

Now inserting `-0 - 1 - 2 - ... - n = -s` gives us:

$$s = n + n^2 - s \leftrightarrow s = \frac{n(n + 1)}{2}$$

## Arithmetic Sum
This time the beginning of the sum is defined as `a`, the end as `b` and the number of terms as `n`. Also the variable `c` defines the difference between 2 consecutive number yet it isn't needed for the end result.

$$s = (a + 0c) + (a + 1c) + (a + 2c) + ... + (a + (n - 2)c) + (a + (n - 1)c)$$

Notice we go all the way up to `n - 1` because we start at `0`. By adding the sum above and the same sum with flipped iteration we land onto the result.

$$s = (a + 0c) + (a + 1c) + (a + 2c) + ... + (a + (n - 2)c) + (a + (n - 1)c)$$

$$s = (b - 0c) + (b - 1c) + (b - 2c) + ... + (b - (n - 2)c) + (b - (n - 1)c)$$

$$2s = (a + b) + (a + b) + (a + b) + ... + (a + b) + (a + b)$$

The term appears `n` times so we land on the definition:

$$s = \frac{n(a + b)}{2}$$

## Geometric Sum
The difference between geometric and arithmetic is that rather than being a constant difference like $$(a + 1c) + (a + 2c) + ...$$ it becomes a ratio like $$(a + c^1) + (a + c^2) + ...$$

$$s = (ac^0) + (ac^1) + ... + (ac^{n - 2}) + (ac^{n - 1})$$

To get the sum we simply multiply both sides by `c` and add `a`.

$$cs + a = a + (ac^1) + (ac^2) + ... + (ac^{n - 2}) + (ac^{n - 1}) + (ac^n)$$

Notice we can substitute the sum `s` into the equation.

$$cs + a = s + ac^n \leftrightarrow s = \frac{ac^n - a}{c - 1}$$

Thus we have the first form of the equation. But $$ac^n$$ takes up a lot of resources to calculate so we define the last integer as `b`:

$$b = ac^{n - 1} \rightarrow bc = ac^n$$

Which then allows us to simply multiply the last integer with the ratio. Way better than the power.

$$s = \frac{bc - a}{c - 1}$$

# Link between CS and Maths
By using math to solve problems we can grant ourselves a higher level of unterstanding of the problem and the ability to transform the solution to a faster algorithm. Below are a few examples.

## Iterational
Here is a example problem: You have a bag of numbers from 1 to 100. 1 number gets taken out. Which is it? I would be inclined to just loop through the array and see where the index doesn't match. Like so:
{% highlight cpp %}
int i = 1; // Missing number
for (; i < bag.size(); ++i)
    if (bag[i - 1] != i) // Check if number matches index
        break;
{% endhighlight cpp %}

But as it turns out this approch is in average slower compared to the alternative. You basically have a `if` statement causing the utilization of branch predictions for each element of the array.

But there is another way to solve the problem. Rather than looking at it through the lens of a programmer look through using the lens of a mathematician. To solve this problem you would take the sum from 1 to 100 and subtract is with the sum of the bag: $$i = s - s_{\text{bag}}$$. Which translated to C++ looks as follows:
{% highlight cpp %}
const auto i = bag.size() * (bag.size() + 1) / 2 - std::accumulate(bag.begin(), bag.end(), 0);
{% endhighlight cpp %}
Wait, we are iterating through the whole array to take the sum, thus rendering the programm slower than before. While it's true that we are iterating through the whole array, notice we aren't using any `if` statements. The CPU tends to favour such implementations since it can make full use of `SIMD` operations, which drastically improve the speed of the program.

I ran a few benchmarks and here are the results:
1. Missing number: 55
```
------------------------
Benchmark           Time
------------------------
BM_prog          26.6 ns
BM_math          8.80 ns
```

2. Missing number: 97
```
------------------------
Benchmark           Time
------------------------
BM_prog          46.0 ns
BM_math          8.77 ns
```

3. Missing number: 4
```
------------------------
Benchmark           Time
------------------------
BM_prog          2.21 ns
BM_math          8.85 ns
```

4. Missing number: 17
```
------------------------
Benchmark           Time
------------------------
BM_prog          8.77 ns
BM_math          8.79 ns
```

Around the number 17 the speed is relatively the same when comparing both programms. But below 17 the first algorithm works faster and above the second algorithm works faster. Also notice the speed of the second algorithm is naturally constant since we basically doing the same operation repeatedly.

## Recursive
We have a recursive formula defined as $$f(x) = 2f(x) - 3$$. What is the result of the 4th recursion when inserting for $$x = 5$$? Computing this with recursive functions would make the solution look like this:
{% highlight cpp %}
int f(int n, const int x)
{
    if (n == 0)
        return x;

    return 2 * f(n - 1, x) - 3;
}
{% endhighlight cpp %}
This leads us to the result `f(4, 5) = 35`. But how would we solve this mathematically? Let's unpack the function.

$$f(x) = 2f(x) - 3$$

$$f(x) = 2(2f(x) - 3) - 3 = 2^2f(x) - 3 * 2 - 3 * 1$$

$$f(x) = 2^2(2f(x) - 3) - 3 * 2 - 3 * 1 = 2^3f(x) - 3 * 2^2 - 3 * 2 - 3 * 1$$

We can notice a pattern being generated when we recursively call the function. Could we generalize it? We define a function $$f^n(x)$$ which inserts `x` based on the recursion counter `n`.

$$f^n(x) = 2^nx - 3(2^{n - 1} + 2^{n - 2} + ... + 2^{n - n})$$

It seems we encountered a sum. But we recognize this is a geometric sum. Thus we get:

$$2^{n - 1} + 2^{n - 2} + ... + 2^{n - n} = 2^{n - n} + ... + 2^{n - 2} + 2^{n - 1} = \frac{ac^n - a}{c - 1}$$

With $$a = 2^{n - n} = 1$$ and $$c = 2$$ we get:

$$2^{n - 1} + 2^{n - 2} + ... + 2^{n - n} = \frac{1 * 2^n - 1}{2 - 1} = 2^n - 1$$

Recognize it? It's the formula for calculating the maximum value of a unsigned integer.

Inserting the above gives us:

$$f^n(x) = 2^nx - 3(2^n - 1) = 2^nx - 3 * 2^n + 3 = 2^n(x - 3) + 3$$

And $$f^4(5) = 2^4(5 - 3) + 3 = 35$$ coincides with the answer of the recursive function from the program. Computing this look like this:
{% highlight cpp %}
int f(uint32_t n, int x)
{
    return (1 << n) * (x - 3) + 3;
}
{% endhighlight cpp %}
Naturally this is faster since no recursion occurs.

# Conclusion
While it's not always worth to observe a problem with a mathematical lense, it could help you out in sticky situations. It opens up a door to better understanding and opportunity for further optimization. I will definitely look forward to collage and what they have to offer to further my understanding of this particular topic.