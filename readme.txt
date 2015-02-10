=====================================================
About OpenSlopeOne
=====================================================

OpenSlopeOne is an implementation of Slope One based on PHP&MySQL, it's an open source project under GPL V3.

It aims to a fast way to use Slope One with PHP&MySQL, and it can handle tons of data.

It's localed on Google Code:http://code.google.com/p/openslopeone/

it uses Zend_Db as it's database layer.PHP5&MySQL5 or higher.

=====================================================
About Slope One
=====================================================

Slope One is a family of algorithms used for Collaborative filtering(http://en.wikipedia.org/wiki/Collaborative_filtering) 

introduced in Slope One Predictors for Online Rating-Based Collaborative Filtering by Daniel Lemire(http://www.daniel-

lemire.com/fr/abstracts/SDM2005.html) and Anna Maclachlan.

You can check http://en.wikipedia.org/wiki/Slope_One to know more about Slope One.

=====================================================
What's the difference between OpenSlopeOne and Vogoo?
=====================================================

As you know,there is also another implementation of Slope One based on PHP&MySQL,Vogoo

(http://sourceforge.net/projects/vogoo),what's the difference?

OpenSlopeOne aims to a fast way to use Slope One with PHP&MySQL, so it cares more about performance.

OpenSlopeOne has two modes to init the slope one table, one is based on pure PHP, the other is based on MySQL procedure, as 

you know, it will be much faster, and you can use it with any other programming language.

the bottleneck of Vogoo(read the source code of cronslope.php Line 70~Line 150, version 2.2) is whether the record exits or 

not. If there is tons of data, the performance is very bad.

In OpenSlopeOne, first I get distinct item ids, then foreach item id, i calculate the slope one of it. I do not have to check 

whether if it exits, and i am faster.

=====================================================
Installation
=====================================================

1. Modify the config ini file: config.ini.php

   ; <?php exit; ?> DO NOT REMOVE THIS LINE
   [database]
   host                 = localhost ; database host name or ip
   username             = root      ; database user name
   password             =           ; user password
   dbname               =           ; database name
   port                 = 3306      ; database host port,3306 as default
   adapter              = PDO_MYSQL ; PDO_MYSQL or MYSQLI

2. Create user's rating table:

    CREATE TABLE IF NOT EXISTS `oso_user_ratings` (
      `user_id` int(11) NOT NULL,
      `item_id` int(11) NOT NULL,
      `rating` decimal(14,4) NOT NULL default '0.0000',
      KEY `item_id` (`item_id`),
      KEY `user_id` (`user_id`,`item_id`)
    ) ENGINE=MyISAM DEFAULT CHARSET=utf8;

there is a sample data file:sample.data, you can load it into the table

    load data infile 'sample.data' into table oso_user_ratings;

3. Create slope one table:

    CREATE TABLE IF NOT EXISTS `oso_slope_one` (
      `item_id1` int(11) NOT NULL,
      `item_id2` int(11) NOT NULL,
      `times` int(11) NOT NULL,
      `rating` decimal(14,4) NOT NULL
    ) ENGINE=MyISAM DEFAULT CHARSET=utf8;

=====================================================
Usage
=====================================================

The main also the only PHP file is OpenSlopeOne.php, you must include it in your own PHP file:

    require './OpenSlopeOne.php';

    $openslopeone = new OpenSlopeOne();

1. Init the slope one table:

before you get the recommendtion, you must pre-computation the slope one table.

    $openslopeone->initSlopeOneTable($factory);

you can specify the mode use 'PHP' or 'MySQL',If you user 'PHP' mode, it's a pure php implementation, and it might be very 

slow when there is tons of data.You can also use 'MySQL' mode, it's based on mysql procedure, and it will be mutch faster.

If you have tons of data to pre-computation, a good advice is that you do not index any colum on oso_slope_one before it 

done.

2. Get recommended items by item's id

    $recommendedItems = $openslopeone->getRecommendedItemsById(9527);

3. Get recommended items by user's id

    $recommendedItems = $openslopeone->getRecommendedItemsByUser(30002);


