---
layout: post
title: 'Set-Based Operations: They''re Not Just For Databases'
date: 2012-01-08 18:45:00.000000000 -06:00
categories: math sets
---
What do you think of when you hear the term <em>set-based operations</em>? I have always thought of that as a database concept. Set-based operations address or operate on multiple data elements, seemingly in parallel, as opposed to iterating through and executing operations one by one. I was introduced to this concept in the context of SQL in relational databases, and it was a struggle at first. My brain was more naturally predisposed to think in terms of iterating over a list, doing one thing at a time.

I remember first running into this mismatch of approaches writing my first SQL Server triggers. (Let's pretend for the moment that this is not a holy war topic.) I intuitively wanted to do a particular thing with each row that was inserted into a table. I wanted to deal with the inserted column values as scalar values.
<pre class="prettyprint">update inserted_row set full_name = @first_name + ' ' + @last_name</pre>
My problem was that since an INSERT can affect multiple rows, and a trigger fires once per INSERT, an INSERT trigger has to handle all the inserted rows at once. So of course the mechanism provided is the INSERTED virtual table rather than a group of variables like I wanted.
<pre class="prettyprint">update people
set full_name = people.first_name + ' ' + people.last_name
from inserted join people on inserted.id = people.id</pre>
I had to learn to think in terms of sets instead of items. In my group of very young and inexperienced developers, there were several that really struggled with making this leap.

This was in the good ole days before SQL Server supported cursors. (Sorry, I'm really not to trying to incite a riot here.) So for quite a few years there, I thought of executing operations on multiple data elements as being done in a set-based manner in SQL,
<pre class="prettyprint">select sum(salary) from employees</pre>
and iteratively in the other languages I used, such as VB, JavaScript, C#, etc.
<pre class="prettyprint">var total = 0.0;
foreach (var employee in employees)
    total += employee.Salary;</pre>
Then along came lambda expressions. This is of course faulty chronology, since lambda expressions were introduced well before I was born. But it was years later that dear Ruby introduced me to the beauty of lambdas. They afford us the ability to specify what needs to be done with each element without bothering ourselves with the particulars of crawling through lists.
<pre class="prettyprint">employees.map{|employee| employee.salary}.inject(:+)</pre>
And of course they found their way into .NET as well.
<pre class="prettyprint">employees.Sum(employee =&gt; employee.Salary)</pre>
This syntax is more concise, more declarative, more about intention than implementation. This is all good, but is it anything more than syntactic sugar? I'm a syntactic sweet-tooth myself, but there is more value here than that. We have separated the concern of getting the data we want, from that of dealing with a particular looping construct or iteration implementation. The same lambda can be used regardless of whether we want to iterate through the list serially, spawn threads to execute in parallel, wrap the operations in a transaction, or whatever.

I have recently been programming some <a href="http://en.wikipedia.org/wiki/Matrix_math" target="_blank">matrix math</a>, which has got me thinking about set-based or matrix-based operations as opposed to the more conventional iterative approach. Your geek core might be harder than mine, but I had not used matrices and vectors since high school. In case you don't want to go do any additional reading on matrices, here are a few insultingly simple examples that should be fairly intuitive to read:

1. Multiply a matrix and a scalar:
<pre class="prettyprint">| 3  1  5 |                   | 6  2 10 |
| 0  2  2 |    *    2    =    | 0  4  4 |
| 6  3  4 |                   | 12 6  8 |</pre>
2. Multiply a column vector and a row vector:
<pre class="prettyprint">| 2 |                             | 2  4  6 |
| 0 |    *    | 1  2  3 |    =    | 0  0  0 |
| 4 |                             | 4  8 12 |</pre>
3. Multiply a row vector and a column vector (note matrix multiplication is not commutative):
<pre class="prettyprint">                    | 2 |
| 1  2  3 |    *    | 0 |    =    | 14 |
                    | 4 |</pre>
I was pleasantly surprised to find matrix support libraries for pretty much every language I thought to check for them. Just for fun, let's look at two ways to solve each of these simple examples, each with a different language.
<h3>Example 1 in Ruby</h3>
Iterative approach:
<pre class="prettyprint">matrix = [[3, 1, 5], [0, 2, 2], [6, 3, 4]]
for i in 0..2
  for j in 0..2
    matrix[i][j] *= 2
  end
end
# matrix =&gt; [[6, 2, 10], [0, 4, 4], [12, 6, 8]]</pre>
Matrix approach using the core Matrix library:
<pre class="prettyprint">require 'matrix'
matrix = Matrix.rows([[3, 1, 5], [0, 2, 2], [6, 3, 4]])
p matrix * 2 #=&gt; Matrix[[6, 2, 10], [0, 4, 4], [12, 6, 8]]</pre>
<h3>Example 2 in C#</h3>
Iterative approach:
{% raw %}
<pre class="prettyprint">int[] column = {2, 0, 4};
int[] row = {1, 2, 3};
var product = new int [3, 3];
for (var i = 0; i &lt; 3; i++)
    for (var j = 0; j &lt; 3; j++)
        product[i, j] = column[i] * row[j];
// product =&gt; {{2, 4, 6}, {0, 0, 0}, {4, 8, 12}}</pre>
{% endraw %}
Matrix approach using the <a href="http://numerics.mathdotnet.com/" target="_blank">Math.NET Numerics</a> library:
<pre class="prettyprint">var column = new DenseMatrix(3, 1);
column.SetColumn(0, new double[] {2, 0, 4});

var row = new DenseMatrix(1, 3);
row.SetRow(0, new double[] {1, 2, 3});

var product = column * row;
// =&gt; 2,4,6
//    0,0,0
//    4,8,12</pre>
<h3>Example 3 in Python</h3>
Iterative approach:
<pre class="prettyprint">row = [1, 2, 3]
column = [2, 0, 4]
product = 0

for i in range(3):
    product += column[i] * row[i]

# product =&gt; 14</pre>
Matrix approach using the <a href="http://numpy.scipy.org/" target="_blank">NumPy</a> library:
<pre class="prettyprint">from numpy import *

row = matrix('1 2 3')
column = matrix('2; 0; 4')
product = row * column #=&gt; [[14]]</pre>
You can see how this approach eliminates a layer or two of loops from your code as well as the accumulation code of adding up sums or whatever. There may not be a huge benefit in these simple cases, but when addressing problems of more realistic complexity this can work wonders for both readability and performance. I have used these matrix constructs mostly in the context of algebraic <a href="http://en.wikipedia.org/wiki/Summation" target="_blank">summations</a> over matrix elements, which are used heavily in various machine learning algorithms such as <a href="http://en.wikipedia.org/wiki/Linear_regression" target="_blank">linear regression</a> and <a href="http://en.wikipedia.org/wiki/Artificial_neural_network" target="_blank">neural networks</a>.

The next time you start to write some looping code, take a second to think about whether there might be another way to go. Chances are that you can accomplish the same thing with less code that will read better and often run faster.
