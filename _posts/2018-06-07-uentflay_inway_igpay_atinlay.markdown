---
layout: post
title:      "Uentflay inway Igpay Atinlay "
date:       2018-06-07 14:50:46 +0000
permalink:  uentflay_inway_igpay_atinlay
---


I never really knew what Pig Latin was before I started the Sinatra MVC Lab, but I guess you can call me fluent now.

This lab focused on building an entire MVC, and I have to admit I found it challenging. For one, my studying schedule over the last month or so has been quite irregular for me due to lots of travel. Secondly, I felt like I was writing Ruby code for the first time in a long time, since the previous section focused on HTML and CSS. 

After familiarizing myself with Pig Latin on Wikipedia, I looked at the piglatinizer_spec.rb file. I saw that numerous examples would be tested, so my code would have to account for several different rules:

1) Words that start with a vowel will simply have "way" added to the end of the word.
2) Words that start with one consonant will be Pig-Latinized by having the first letter moved to the end of the word, and then adding "ay" to the end. (i.e., boots would become ootsbay)
3) Words that start with more than one consonant need to have all those consonants moved to the end of the word, and then have "ay" added (i.e., bread becomes eadbray, spray becomes ayspray)

It took me a lot of trial and error and I made the mistake of jumping into coding way too early without thinking things through. I stepped back and saw that I needed a way to check whether letters were consonants or vowels, since that determined the rules of Pig Latin. In my PigLatinizer class, I wrote:

```
  def vowel(letter)
    letter.match(/[aeiouAEIOU]/)
  end
```

I realized I had to account for capital letters, too, since one of the tests used the word "Enumerator." 

I also created methods that would return the removed letter(s) from words that began with consonants:

```
def first_letter(word)
    word.slice!(0)
  end

  def second_letter(word)
    word.slice!(0..1)
  end

  def third_letter(word)
    word.slice!(0..2)
  end
```

The first method's return value will be the first character, the second method's return value will be the first two characters, and the third method's return value will be the first three characters. In all three methods, word is being destructively changed because the slice! method is called. 

That took care of the testing and setup of any user-inputted word(s). Next came the fun part: the actual Pig-Latinizing. 

The lab requires you to write methods that will Pig-Latinize a single word, as well as a phrase. I wrote the method for the single-word first, since I figured the method for the phrase would incorporate the fundamentals of the single-word method.

```
def piglatinize_word(text) #to Pig-Latinize a single word
    @text = text
    if vowel(@text[0]) 
      "#{@text}way"
    elsif !vowel(@text[0]) && !vowel(@text[1]) && !vowel(@text[2])
      "#{@text}#{third_letter(text)}ay"
    elsif !vowel(@text[0]) && !vowel(@text[1]) 
      "#{@text}#{second_letter(text)}ay"
    elsif !vowel(@text[0]) 
      "#{@text}#{first_letter(text)}ay"
    end
  end
```

The third line checks to see if the first letter (in index 0) is a vowel, using the previously written #vowel method. If it is, it takes the word and simply adds "way" to the end. 

The fifth line checks to see if the first, second, AND third letters are *not* vowels. If they're consonants, then it takes the newly-destructed word, adds the first three letters from the original word, and tacks on "ay." The seventh and ninth lines of code make similar checks, but for the first two letters.

With this code laid out, I could tackle the method for Pig-Latinizing a phrase. It would follow the same rules that a Pig-Latinizing a single word would follow. However, I had to first use the #split method on the string to ultimately create an array with each individual word as a single element in the array. The array would have to be iterated over, and each word would get Pig-Latinized. Finally, each newly Pig-Latinized word would have to be put back into a string:

```
def piglatinize_words(text) #to Pig-Latinize a phrase
    @text = text.split(" ") 
    @text.collect do |word|
      piglatinize_word(word) 
    end.join(" ") #creates string
  end
```

Finally, the PigLatinizer class needed a general #piglatinize method. This method would basically check to see how many words existed in the user's input, and then calling on the appropriate Pig-Latinizing method (#piglatinize_word or #piglatinize_words):

```
def piglatinize(text)
    if text.split(" ").length == 1
      piglatinize_word(text)
    else
      piglatinize_words(text)
    end
  end
```

The user_input.erb file would have the HTML for the form where a user would submit the word(s) they'd like Pig-Latinized. 

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Pig Latinizer</title>
  </head>
  <body>
    <h1>Pig Latinizer!</h1>
    <form action="/piglatinize" method="post">
      <p>Enter your phrase: </p>
      <textarea name="user_phrase"></textarea>
      <br>
      <button type="submit" value="submit">Submit</button>
    </form>

  </body>
</html>

```

The key things here are the form's action and method attributes, and the name attribute for textarea. Here, the POST method is being used, and the user's input will post to /piglatinize. The textarea's name will serve as the key in the params hash, which is referred to in the application controller file (app.rb). 

The app.rb file contains the routes. It also needs to get linked to the file where the model's code is located (models/piglatinizer.rb). 

```
require_relative 'config/environment'
require_relative 'models/piglatinizer.rb'

class App < Sinatra::Base

  get '/' do
    erb :user_input
  end

  post '/piglatinize' do
    @piglatin_text = PigLatinizer.new.piglatinize(params[:user_phrase])

    erb :results
  end

end
```

Here, I have the post method and the /piglatinize path. In that method, I've assigned the @piglatin_text instance variable to a new instance of PigLatinizer that calls on the #piglatinize method. It has an argument of (params[:user_phrase]), which is the value of whatever word(s) the user inputs. The :user_phrase comes from the name attribute in our form's textarea tag. 

Then, the results.erb file is rendered. In this lab, this file is the one that shows the Pig-Latinized word. My code here was simple:

```
<h1>Your Pig Latinized Text</h1>
<p><%= @piglatin_text %></p>
```

Inside the ERB tags is @piglatin_text, the same instance variable that was in the #post method in app.rb. This instance variable's content is simply the Pig-Latinized text. 

I feel like I definitely need more practice to wrap my head around these various components and how everything is related to each other, but this was a great way to build an MVC from scratch.

