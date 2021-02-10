# xy2xy - yandex inner technologies analog list

TLDR: xy2xy is [repository](https://github.com/kirillsulim/xy2xy) with yandex technologies and its analogs


## xg2xg - a simple list of inner google technologies

Couple of years ago I found [xg2xg](https://github.com/jhuangtw/xg2xg) repository in github.
I was fascinated how many projects there is inside google.

I was interested why there is no list of yandex technologies.
So I've started to check all articles in [yandex official blog](https://habr.com/ru/company/yandex/) and repositories in [yandex github page](https://github.com/yandex) and collect all technologies in list.
And that was the second time to be fascinated.
Yandex have a lot of open-source projects.


## For who is xy2xy?

I think there are 3 types of people who can use this list:

### 1. People who have worked in Yandex and now leave to another company

You cannot use inner Yandex technologies in another company.
But sometimes you need any message broker or any map-reduce.
It can be inner technology, open-source project or commercial solution.
And this list can help you find alternatives.

### 2. People with experience who are starting to work at yandex

You can use open-source technologies in Yandex of course, but in many cases inner technologies are better integrated in infrastructure.
So try to use inner technologies if you can.

### 3. People who want to work in Yandex or any big company

When I was starting my career it was very useful for me to understand what technologies big companies uses.
I've started to read about continuous integration, git-flow, map-reduce and another good practices of software development, and it helped me a lot to understand what technologies should I learn to be hired.
I think if you learn open-source analogs if Yandex inner technologies you are interested in your knowledge will help you with your career in Yandex or any big tech-company.


## Markdown tables

I've created my repository and started to fill tables.
And after some time I've understood: my .md file is a mess.
So I've tried to use HTML tables and this partially solve my problem.
My file was huge, but I was able to understand what is the data in my table.
Then I've added commentary with column name to my `<td>` tag, and it resolves the issue.
Now I always understand what column I edit.

So I recommend to use HTML for all non-trivial markdown tables.
Those tables are still complicated but are readable in source code.


## What about NDA?

There is a simple rule to check if inner technology is part of NDA or not.
If there is post which describe technology on official company blog or YouTube channel or technology is presented in official github repository
that means the company itself do not consider this technology as a part of NDA and you can add it to open-source list.


## Contribution

Feel free to add any inner Yandex technology which is not a part of NDA.
Please follow contribution guidelines of the xy2xy repository.
