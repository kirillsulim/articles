# xy2xy - yandex inner technologies analog list

TLDR: xy2xy is [repository](https://github.com/kirillsulim/xy2xy) with list of yandex inner technologies and its analogs


## xg2xg - a list of inner google technologies

Couple of years ago I found [xg2xg](https://github.com/jhuangtw/xg2xg) repository on github.
I was fascinated how many projects there is inside google.

This summer because of COVID-19 I've had more free time than usual and decided to create similar list for Yandex inner technologies.
I've tried to find it, but there wasn't such list, so I've created [xy2xy repository](https://github.com/kirillsulim/xy2xy) to fill it with Yandex inner technologies list.
I was interested why there is no list of yandex technologies.
I've checked articles from [yandex official blog](https://habr.com/ru/company/yandex/) and repositories in [yandex github page](https://github.com/yandex) and collect all interesting technologies in this list.
And that was the second time to be fascinated.
Yandex have a lot of open-source projects I didn't hear of.


## Markdown tables

At the start of my project I wanted to use .md syntax for tables as it is made such way in xg2xg.
But after I've added a dozen of technologies to my list I understand: my .md file is a mess because of a big amount of information in each cell.
I've tried to switch to HTML syntax for tables and this partially solve this issue.
My file was bigger, but I was able to understand what is the data in my table.
Then I've added commentary with column name to my `<td>` tag, and it resolves the issue.
Now I always understand what column I edit.

So I recommend to use HTML for all non-trivial markdown tables.
Those tables will be bigger, but it will also increase readability of source code.


## For who is xy2xy?

There are 3 types of people that list can help:

### 1. Ex Yandex developers

If you've used some inner Yandex technology to solve some tasks, and then you have similar tasks outside Yandex you have to look for an alternative.
You can use open-source project or commercial solution instead of inner Yandex technology.
And this list can help you find alternatives.

### 2. Experienced developers who are starting to work at Yandex

You can use open-source technologies in Yandex of course, but in many cases inner technologies are better integrated in infrastructure.
So try to use inner technologies if you can.
xy2xy will help you find Yandex alternatives to technologies you have experience with.

### 3. Developers who want to work at Yandex

When I was starting my career I was interested in technologies of big companies.
I've read a lot about best practices of software development.
For example, I learned about continuous integration and continuous delivery and Jenkins as an example of open-source technology which provides it.
Also, I've read about Map-Reduce technology and what tasks you can solve with it.
In the log run I all helped me to understand what should I learn to be hired as a developer in big tech company. 

If you learn open-source analogs of inner Yandex technologies you are interested in your knowledge will help you with your career in Yandex or any big tech company.


## What about NDA?

There is a simple rule to check if inner technology is part of NDA or not.
If there is post which describe technology on official company blog or YouTube channel or technology is presented in official github repository
that means the company itself do not consider this technology as a part of NDA and you can add it to open-source list.


## Contribution

If you are still interested please visit [xy2xy](https://github.com/kirillsulim/xy2xy) page.
Feel free to add any inner Yandex technology which is not a part of NDA.
Please follow [contribution guidelines](https://github.com/kirillsulim/xy2xy#contibuting) of the xy2xy repository.
