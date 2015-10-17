---
layout: post
title: "XMLSchema and ISO-8601: not a match made in heaven"
date: 2014-02-03 17:41
comments: true
published: true
categories: [ php ]
---

Ever used the ISO-8601_ standard to format dates in, for example, your API? And ever thought you'd be "safe" for all
client applications, because you adhere to an ISO standard?

Then I got a little surprise for you.

.. code-block:: xml

    <xs:element name="date_added" type="xs:dateTime" />

.. more

XMLSchema doesn't implement ISO-8601 completely, something I only found out after 3 hours of debugging. When using the
``dateTime`` type, the XMLSchema definition says the following which is somewhat vague::

    This representation simplifies interval arithmetic and leap-year calculation for dates before
    the common era (which may be why astronomers and others interested in such calculations with
    the proleptic Gregorian calendar have adopted it), and is consistent with the current edition
    of [ISO 8601].

What they (should) mean to say is that their accepted input format is one of the formats ISO-8601 describes. The trick
is in the, optional, timezone part. ISO-8601 allows for the timezone part to be just four digits with a plus sign in
front: ``+0900``. This means that a date/time combination could be formatted as follows::

    2014-02-06T07:06:00+0000

This format will, however, not validate through XMLSchema. It expects the most verbose syntax that is allowed by the
standard, which includes a colon ``:`` between the second and third digit of the timezone. This makes kind of sense,
because in most other parts of the standard format, the separator ``:`` or ``-`` can be omitted as well.

The fun part, however, is yet to come. When working in PHP, which I do a lot, there's a problem. In PHP, there's the
``\DateTime`` class to work with, you've guessed it, dates and times.

.. code-block:: php

    <?php
    $dateAndTime = new \DateTime('now');

This class has a ``format()`` method that takes a textual format pattern to determine the format you want. For your
convenience, some preconfigured formats have been defined. One example, which I used to use a lot, was the ISO-8601 one.

.. code-block:: php

    const string ISO8601 = "Y-m-d\TH:i:sO" ;

The documentation says the following about it::

    DATE_ISO8601
    ISO-8601 (example: 2005-08-15T15:52:01+0000)

Fair enough. This adheres to the standard. But when using it to generate an XML output that will be checked using
XMLSchema, **it will just fail**.

The guys at PHP noticed this problem and solved it for us. There's another predefined format, which is called ``W3C``.
It simply adds a ``:`` and you're good to go and use XMLSchema!

.. code-block:: php

    <?php
    $dateAndTime = new \DateTime('now');
    echo $dateAndTime->format(\DateTime::W3C);

    // Will output 2014-02-06T07:06:00+00:00

From now on, I use the ``W3C`` one everywhere. It adheres to the ISO-8601 standard just as well, but obviously allows
for more client application to run without problems!

.. _ISO-8601: http://en.wikipedia.org/wiki/ISO_8601
