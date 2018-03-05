---
layout: post
title:      "My Prime Mistake: Overthinking Code"
date:       2018-03-05 15:58:25 +0000
permalink:  my_prime_mistake_overthinking_code
---


I’ll admit that when I first read the instructions for the Prime? lab, I almost instantly became overwhelmed. I couldn’t use Math, and I was thinking of the infinite number of numbers that exist and trying to write code that would figure out if any integer was prime or not.

I have a horrible tendency to overthink my code and make it way more complicated than it should be, and it was no exception in this lab. I made the rookie mistake of jumping right in and trying to code before having my head straight on what made a number prime or composite.

I stepped on the brakes, erased the mess of code I had typed in my Ruby file, and as the lab itself suggested, I refreshed my memory on what exactly defines a prime number. The key takeaways:

1. 1 and 0 are not prime numbers. Ever.
2. Negative integers are never prime numbers, either.
3. A number is always prime when it can only be divided evenly by 1 or the number itself. This means 2 and 3 are prime, but 4 is not, since it is evenly divisible by not only 1 and 4, but also 2.

Then it was time to tackle the code. The first couple of bits were straightforward:

```
if integer <= 1 
	false
```

That took care of points 1 and 2 above.

Then came the “fun” part: determining whether all other numbers in the universe were prime or not. 

`elsif integer >= 2  #=> ?????`


Since our #prime? method takes in an argument of integer and given the rules of a prime number, creating a range of numbers from 2 to integer made sense to me. :

`range = (2..integer)`

My thinking was that my integer argument would be calculated against this range. The range only needs to go up to the integer itself, regardless of what the integer is, given the rules of divisibility. (I didn’t start the range at 1, since all numbers are evenly divisible by 1. We don't need to check divisibility against 1!)

Next, I wanted to convert this range of numbers into an array that I could iterate over:

`range = (2..integer).to_a`

With that array, I decided to call the #none? method, which returns “true” if the code block never returns true. 

`range.none? do |range_number|`

Next was actually defining the conditions in the code block. So we know a number is prime if it’s only divisible by 1 and the number itself. For example, 5 is prime because the only time it has a remainder of 0 is when it’s divided by 1 or 5. 

So say I passed in 7 as my integer argument. Since I defined my range as (2..integer), the range would only be 2–7. I would need to loop through that range to see if any numbers in that range are evenly divisible with my integer. 

`integer % range_number == 0 `

I also needed to write another condition that tested whether the integer was also equal to the range number. 
	
`integer != range_number`

This condition covers situations where a prime number may pass the first test. Take integer 5, for example. In this loop, 5 will be divided against 2, 3, 4, and 5, and the only time it will return true is when it’s divided by itself. We already know that when a number is evenly divisible only by 1 and itself, it’s prime. Having this line of code alone won't work in correctly identifying prime numbers. We need to add another condition that makes sure that the integer is not equal to the number in the range that it's being operated on. 

At this point I started confusing myself a bit with all the true/false conditions I was writing. To help me visualize my code better, I went old-school and literally wrote out different integer scenarios on a piece of paper:

![](https://lh3.googleusercontent.com/Qio3-CVjOxEhnj1cBdQBH0E8erWMQkNeyhsx4YY3LFIPNyVOv4eET9HKt8CNkGLmf6QO5kj3jmPnAKX4MRr1Vk5E3qE--RUF8xHpEYz41Ui7dQwFL6-gvdUnZV3GIqV64WnKxZwOHKhLFdr_yF4nFuu6Rla2WMZ7WKF6mUKu_QbmIKG--uTEOo9MV2fP1oO84HP9330do4X1FQTRBvve8tP6SLiCyaSdFQ41VxNlyIowqoBVzPWv07xQsdHXld2N1HhcIMH8hk6iSeM__vAdfhkuJY7oxmSGwY512kVCR-YCkf1sfDn3fOCPajTlYHFG25bn8LC-zNisoIQLJb-LJakD4yP3BKpruY5DghvAR_SULbET47SrJnUKFFxiyuMYrRpVYIqNPBMmoySgIadAGzQTDo7o9CT3t5jHT7bK-DfknY5-LdT_jH8uJ9Xye38TLA4gT2ggBrA53W6ohavmdowvNmGqteFWrdHG8Q9r7k-WB88B2zwmZZYe0E3s5qC4YcRL-YeMNDh-0s-cey1fdB3cfGJyIF1pt_LpRhJQIqE7FNki0WpwrHu9GySY22RCIflsg6ENYk7Yip_-ModhM4Kn0gm_UNIRGstHNy-f=w2560-h762-no)

![](https://lh3.googleusercontent.com/S6bxgH8vrMN9ehVkx_ZQYrqcuEZuWgOgrIQVCTKTDtlm0qwo8P61YuPPq9Xdo5TaFW_XjBmxQJSDbSNeRYguM0NG7Ee8Hr-3LkzaIvdxP2DjTjLIE_CTJRcPAIbEgPH2-PAnmLiNo7gJejWQjcNh_ASx0WGBfRNx0e4FgrUM8DB1rVk0N-F-93MRDwBXsLdhElXytdaz8pdUdwdHTtRS0ywgrLR1ne9jY6LwoG0VyQB4gooe2705M0dpwUS9kUEAbFpzKIYElgurUFRVPxZta8QfnuKPB9BQ_jNSSDRKdVtGyXv8jvkzYWCKviLCLitpvW9WKd-0heBSzeP_dm6R3_xdZmrlFAf9bomEI6uSyk8QDKPyuU0KoHC001KjAsPFQczKqjfQCBiYMSCPMHLfbv_n-p7D7LO9udX9iRcXs2jsfMn7NQb6fSkZ6fNGNGARF4yOYH9e4LKqhvfCH-nsFHhzCL97KEt_3lMaU9YIU-j2bW5v2Z_525apx_1nG0j2XEOhXcmPtrb0t1xURtqUana92vekq22aklhB2BIMV8J_lOtqVN05FktELba9mvBzqoN4GY0txUSKlM2C5yIrMmRVxGtAreNsrmPlNtm7=w1612-h1208-no)

Essentially any time an integer is a prime number, it will always pass one condition, but not the other, ultimately making it false. Since we're calling #none?, it returns "true" since the code block will never be true. 

Meanwhile, for any composite integer passed in as an argument, the code block, at some point, return true. It will be true that a number other than 1 and itself will evenly divide into the integer.

Here’s the code I finally ended up with:

```
def prime?(integer)
  if integer <= 1  
    false 
  elsif integer >= 2 
    range = (2..integer).to_a  
    range.none? do |range_number| 
      integer % range_number == 0 && integer != range_number 
    end 
  end 
end
```

I’m sure there is probably a much more eloquent way of writing this code, but it worked! 

