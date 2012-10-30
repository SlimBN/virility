# Virility

Virility calls upon the API's of many popular social services such as Facebook, Twitter and Pinterest to collect the number of likes, tweets, pins etc. of a particular URL.  Written with a modular construction, Virility makes it easy to drop new data collection strategies into the framework so that you can collect all of your statistics in one easy location.

## Installation

Add this line to your application's Gemfile:

    gem 'virility'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install virility

## Usage

The Excitation object will collect the data from all of the available strategies. 

    stats = Virility::Excitation.new("http://rubygems.org")
    stats.poll # returns a hash with the collected output of all data sources
    stats.counts # returns a hash of just the virility counts => {:delicious=>943, :facebook=>72, :pinterest=>0, :plusone=>137, :stumbleupon=>1488, :twitter=>2319} 
    stats.total # returns the sum of all virility counts

One common implementation would be to collect the total count of all social shares for a given URL

    Virility::Excitation.new("http://rubygems.org").total # => 4959

## Individual Strategies

Currently there is support for the following social resources:
* Facebook
* Twitter
* Delicious
* Pinterest
* Google Plus One
* Stumble Upon

Each social resources is implemented as a strategy and contains at least two methods: poll and count.  

__poll__ does the work of querying the API to get the data, __count__ pulls out the individual number of shares for that social.

Let's say you only need to get the number of tweets for a URL, you could use the Virility::Twitter class by itself:

    tweets = Virility::Twitter.new("http://rubygems.org")
    tweets.poll # returns a hash with the collected output from Twitter => {"url"=>"http://rubygems.org/", "count"=>2319} 
    tweets.count # returns the number of tweets for that URL => 2319 

## Facebook Usage

    fb = Virility::Facebook.new("http://rubygems.org")
    fb.poll # returns a hash with the collected output from Facebook 
    fb.count # returns the total_count for that URL => 72 

The Facebook strategy leverages the FQL query against the link_stat table. Because of this, the following data fields are available:
* like_count
* click_count
* share_count
* comment_count
* commentsbox_count
* total_count (used as the default count for all Facebook activity)
* comments_fbid

## Important Notes

URL's are very specific in the context of a social media.  For example, http://rubygems.org will return different results than http://rubygems.org/ with a trailing slash.  Also, http vs https will give you different numbers. This actually has a lot to do with why we created this gem.  When testing be sure to investigate all of the URL variations in order to get the most complete picture of your data.

#### Case Study

Compare the total count results for http://ruby-lang.org/en. One has the trailing slash and one does not.

    Virility::Excitation.new("http://www.ruby-lang.org/en").total # => 247695 
    Virility::Excitation.new("http://www.ruby-lang.org/en/").total # => 253190 

On this particular day, there was a 5,495 count difference between the two values. Inspecting the actual results shows you which of the social networks takes the varying forms of the urls into account:

    Virility::Excitation.new("http://www.ruby-lang.org/en").counts
    # => {:delicious=>37, :facebook=>3, :pinterest=>0, :plusone=>20, :stumbleupon=>246937, :twitter=>698} 

    Virility::Excitation.new("http://www.ruby-lang.org/en/").counts
    # => {:delicious=>4314, :facebook=>813, :pinterest=>22, :plusone=>406, :stumbleupon=>246937, :twitter=>698} 

Stumbleupon and Twitter are consistent while Delicious, Facebook, Pinterest and Google Plus One return different results. Depending on your needs, you could craft an algorithm that takes all of this into account and attempts to deliver an accurate number by combining the data sets that are different and trusting the ones that are the same. 

Based on this logic, it is possible to consider that the true total share count is closer to _253,250_. Not only is this an opinionated number, it's accuracy is questionable based on assumptions, however if you are just trying to get a ballpark feeling of the virility of your content, this number should suffice.

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

## Copyright

Copyright (c) 2012 Jay Sanders. See LICENSE.txt for
further details.