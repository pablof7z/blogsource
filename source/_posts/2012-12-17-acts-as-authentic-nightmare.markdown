---
layout: post
title: "acts_as_authentic nightmare"
date: 2010-06-23 23:10
comments: true
categories:
  - programming
  - ruby on rails
---

So I decided to do a few tests on the Server Protectors’ website and noticed a few annoyances with the register form, I decided to delve in and fix it, just a couple of extra validations, not too much.

Half an hour later I was pretty much ready to do some open heart surgery on authlogic, man is the configuration of act_as_authentic confusing! And of course, RTFMing doesn’t work, there is not a single example of how changing the default act_as_authentic configuration works!

Searching around I found a few persons with the same problem and no solutions, so I thought I should share how I did it, or rather, what I did.

This is the resulting code:

acts_as_authentic do |c|
 c.validates_uniqueness_of_email_field_options = {:if => "false"}
 c.validates_length_of_login_field_options = {:if => "false", :minimum => 4}
 c.validates_format_of_login_field_options = {:if => "false", :with => Authlogic::Regex.login}
 c.validates_length_of_email_field_options = {:if => "false", :minimum => 4}
 c.validates_confirmation_of_password_field_options = {:if => "false"}
 c.validates_length_of_password_confirmation_field_options = {:if => "false", :minimum => 4 }
end
So as you can see a couple of options are a bit lame, like having a minimum when the if is being set to false, well, if you remove the minumum validates_length_of will complain (it makes sense from validates_length_of point of view, after all, if you are never going to be using the validation why call the function at all?)

I hope this helps and saves some time.