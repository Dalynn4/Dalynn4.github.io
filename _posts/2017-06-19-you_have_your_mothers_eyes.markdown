---
layout: post
title:  "You have your mother's eyes."
date:   2017-06-19 22:50:32 +0000
---


In Ruby, we refer to Inheritance as a relationship between two classes. For example, we know that all cats are mammals, and all mammals are animals. The benefit of inheritance is that classes lower down the hierarchy get the features of those higher up, but can also add specific features of their own. If all mammals breathe, then all cats breathe. Ruby only allows a  class to inherit from a single other class. 

Here is how we can express this concept in Ruby: 
```
class Mammal  
  def breathe  
    puts "inhale and exhale"  
  end  
end  
  
class Cat  <  Mammal  
  def speak  
    puts "Meow"  
  end  
end  
```

Though we didn't specify how a Cat should breathe, every cat will inherit that behaviour from the Mammal class since Cat was defined as a subclass of Mammal. (In OO terminology, the smaller class is a subclass and the larger class is a super-class). Hence from a programmer's standpoint, cats get the ability to breathe for free; after we add a speak method, our cats can both breathe and speak.

There will be situations where certain properties of the super-class should not be inherited by a particular subclass. Though birds generally know how to fly, penguins are a flightless subclass of birds. Here, we can override a inherited method in order to make our Penguin class correct.

```
class Bird  
  def preen  
    puts "I am cleaning my feathers."  
  end  
  def fly  
    puts "I am flying."  
  end  
end  
  
class Penguin < Bird  
  def fly  
    puts "Sorry. I'd rather swim."  
  end  
end  
```
  

Rather than exhaustively define every characteristic of every new class, we need only to append or to redefine the differences between each subclass and its super-class. This use of inheritance is sometimes called differential programming. It is one of the benefits of object-oriented programming.
