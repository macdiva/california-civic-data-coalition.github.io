---
layout: post
title: "Package data like software, and the stories will flow like wine"
deckhead: A humble suggestion from your friends at the California Civic Data Coalition
byline: "By [Ben Welsh](http://palewi.re/who-is-ben-welsh/), [Agustin Armendariz](http://www.twitter.com/agustin_NYT) and [Aaron Williams](http://aboutaaron.com)"
published: true
---

The melodrama is so familiar it's mundane. The government is asked to release an important dataset. They dither. We moan. We groan. Sometimes we sue, or even (gasp) organize. More and more, our pushback works. They make good and release the raw data, maybe even posting it online.

Next comes the tedious downloading, extracting, transforming, cleaning and exploring necessary before the creative work can begin. By the time we write the story, build the application or design the graphic, we're mentally spent, eager to move on to the next project. 

The pathetic result: All of the valuable work that prepared the data for analysis is discarded or kept locked away. Every newcomer must reinvent virtually identical tools simply to get started.

This must change. It’s a wasteful exercise so behind the times that even broadsheet newspaper reporters, a faction with revanchist delusions on par with the Putin administration, [see the problem](http://www.nytimes.com/2014/08/18/technology/for-big-data-scientists-hurdle-to-insights-is-janitor-work.html?_r=0).

Open-data hackers should heed the words of [Dave Guarino](http://daguar.github.io/2014/03/17/etl-for-america/) and work together to improve the pipeline that prepares data for meaningful analysis, crucial but unglamourous "plumbing" often overlooked in the rush to build the latest flashy user interface.

We're here to demonstrate a way to make it happen. We call it "pluggable data."

### What we mean

If you have any experience as a developer, you've probably bumped into packaged 
software. Thousands of free and open-source libraries, typically unique to each 
programming language, are available for installation over the web from centralized servers. Command-line tools like [``pip``](http://pip.readthedocs.org/en/latest/index.html) (Python), [``gem``](https://rubygems.org/) (Ruby), [``CPAN``](http://www.cpan.org/) (Perl) and [``npm``](https://www.npmjs.org/) (NodeJS) can make it easy to do. 

For instance, if you are a Python developer interested in trying out the [``requests``](http://docs.python-requests.org/en/latest/) library, installing it on your laptop is as easy as:

{% highlight bash %}
$ pip install requests
{% endhighlight %}

And now using it is only a import away.

{% highlight python %}
>>> import requests
>>> requests.get("http://www.californiacivicdata.org/").status_code
200
{% endhighlight %}

The concept has been expanded by [web frameworks](http://en.wikipedia.org/wiki/Web_application_framework) like [Django](http://www.djangoproject.com/) to package not just freestanding utilities like ``requests``, but entire applications that can be dropped into the framework's rigid system and ["just work."](https://www.youtube.com/watch?v=qmPq00jelpc)

This approach, [championed](http://www.b-list.org/weblog/2007/nov/29/django-blog/) [eloquently](https://www.youtube.com/watch?v=A-S0tqpPga4) by Django leaders like James Bennett, is sometimes called "pluggable" or ["reusable"](https://docs.djangoproject.com/en/dev/intro/reusable-apps/), because its modular design makes it portable to a wide range of sites.

A good example is the [Pinax project](http://pinaxproject.com/), which provides Django-ready components that furnish common features like [comments](https://github.com/eldarion/dialogos), [badges](https://github.com/eldarion/brabeion), [phone confirmations](https://github.com/pinax/pinax-phone-confirmation) and [user accounts](https://github.com/pinax/django-user-accounts). Each contains code that builds database tables, configures administration panels and spells out application logic that can interact with users.

Our proposal is to bring the same approach to packaging data. If a series of simple installation commands can provide a Django application with everything necessary to build a social networking site, why can't it also provide [U.S. Census statistics](http://factfinder2.census.gov/faces/nav/jsf/pages/download_center.xhtml), [the massive federal database that tracks our country's chemical polluters](http://www2.epa.gov/toxics-release-inventory-tri-program/tri-basic-data-files-calendar-years-1987-2012) or something as simple as [a list of every U.S. county](http://www.epa.gov/envirofw/html/codes/state.html)? 

### How we do

With that idea in mind, a small group of programmers from the [Los Angeles Times Data Desk](http://www.latimes.com/local/datadesk/), [The Center for Investigative Reporting](http://cironline.org/) and Stanford's new [Computational Journalism Program](http://towcenter.org/blog/data-journalist-profile-cheryl-phillips-stanford-data-journalism/) met for two days last month at Mozilla offices in San Francisco.

Under the newly minted banner of the California Civic Data Coalition, we set out to package and refine [raw data from CAL-ACCESS](http://www.sos.ca.gov/prd/cal-access/), the state of California’s campaign finance and lobbying activity database.
 
Thanks to a [successful organizing effort](http://maplight.org/content/73249) last year, Secretary of State Debra Bowen [committed](http://www.sos.ca.gov/admin/press-releases/2013/db13-035.htm) to posting a nearly complete dump of the data online, updating it on a regular basis. 

Weighing in at more than 650 megabytes, it contains 76 database tables and nearly 35 million records. 

In the past, slices were only released on demand, for a small fee, [via compact disc](/img/calaccess-cd.png). Analysts, including one of the authors of this post, have spent months learning how to negotiate the system's contours, overcome its quirks and [grind out a story](http://cironline.org/reports/california-speaker-gives-assemblys-juiciest-jobs-biggest-fundraisers-4501)&mdash;only to abandon all of the code that made it happen when they moved on to the next topic.

Now that the dump is freely available and open to all, it offers an opportunity to pool efforts. Even though we represent rival media outlets, we'd rather compete at analyzing the data than downloading and parsing it. And we believe that the nature of open development will encourage us to write better code and documentation.

Today we're ready to announce the release of ``django-calaccess-raw-data``, our first pluggable Django dataset, [hosted on GitHub](https://github.com/california-civic-data-coalition/django-calaccess-raw-data) and distributed via [the Python Package Index](https://pypi.python.org/pypi/django-calaccess-raw-data/). With a few simple commands, you can download the data, transform it into clean CSV files and then load it into a MySQL database.

Assuming you have [a basic Django project](https://docs.djangoproject.com/en/1.6/intro/tutorial01/) already configured, here's all it takes. First, install the pluggable application from the Python package repository.

{% highlight bash %}
$ pip install django-calaccess-raw-data
{% endhighlight %}

Add it to the ``INSTALLED_APPS`` list in Django's standard ``settings.py`` file, as you would any other application.

{% highlight python %}
INSTALLED_APPS = (
    # ... other apps up here ...
    'calaccess_raw',
)
{% endhighlight %}

Make sure that your MySQL installation can use the brutally effective, and 
tragically underused, [``LOAD DATA INFILE``](http://dev.mysql.com/doc/refman/5.1/en/load-data.html)
command by adding the following to the database configuration also found in ``settings.py``.

{% highlight python %}
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'my_calaccess_db',
        'USER': 'username',
        'PASSWORD': 'password',
        'HOST': 'localhost',
        'PORT': '3306',
        # Here's the thing we're talking about
        'OPTIONS': {
            'local_infile': 1,
        }
    }
}
{% endhighlight %}

In the same file, shut off Django's safety valve so ``LOAD DATA INFILE`` can run wild.

{% highlight python %}
DEBUG = False
{% endhighlight %}

Sync your database to create the CAL-ACCESS tables.

{% highlight bash %}
$ python manage.py syncdb
{% endhighlight %}

And, finally, run the custom management command that will download, parse, clean and load all of the data.

{% highlight bash %}
$ python manage.py downloadcalaccessrawdata
{% endhighlight %}

That's it. You now have the state's full database, including a set of administration panels.

You could use it to track the millions of dollars flowing into this November's governor's race, investigate what lobbyists are up to this session at the statehouse or impress everyone by designing a sophisticated analysis that stretches back over the nearly 15 years of data in the system to quantify the influence of money in California politics. 

Of course, to do any of that, you'll need to further regroup, filter and refine the data. But at least the initial headaches are out of the way, and any work you build on top of our application could be packaged and distributed in the exact same way.

In that scheme, our raw data application is simply one of your new package's dependencies, much in the same way that the ``requests`` library we installed earlier depends on components of [``urllib3``](http://urllib3.readthedocs.org/en/latest/).

An example is already taking shape in the [``django-calaccess-campaign-browser``](https://github.com/california-civic-data-coalition/django-calaccess-campaign-browser) repository, where our team is experimenting with a set of further-refined tables and a simple web application for exploring campaign filings aimed at power users, like statehouse reporters and newsroom analysts, who want a more flexible interface than the [helpful but fundamentally limited](http://dbsearch.ss.ca.gov/ContributorSearch.aspx) closed-source sites that are now the only way to interact with this database online.

### Where you come in

If you are interested in this effort and would like to contribute, here's how you can help today.

1. Download and install [``django-calaccess-raw-data``](https://github.com/california-civic-data-coalition/django-calaccess-raw-data) or [``django-calaccess-campaign-browser``](https://github.com/california-civic-data-coalition/django-calaccess-campaign-browser). Report bugs.
2. Fork our code and try to close one of the [many](https://github.com/california-civic-data-coalition/django-calaccess-raw-data/issues) [tickets](https://github.com/california-civic-data-coalition/django-calaccess-campaign-browser/issues) we've filed. If you're knowledgable about how CAL-ACCESS works, we need your help!
3. Try to package and distribute an open data set you've worked with. If you write Python, the [Django documentation](https://docs.djangoproject.com/en/dev/intro/reusable-apps/) and [Scott Torborg](http://www.scotttorborg.com/python-packaging/minimal.html) have excellent tutorials that can introduce you to the process.

If nothing else, [watch James Bennett's excellent 2008 talk](https://www.youtube.com/watch?v=A-S0tqpPga4) on designing reusable applications and ask yourself, every time you start a new project, how you could package it for future reuse. It's a simple but powerful approach that has multiplied the reach and reuse of open source, and we hope can do the same for open data.
