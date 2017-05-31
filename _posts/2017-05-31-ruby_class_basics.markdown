---
layout: post
title:  Ruby Class Basics
date:   2017-05-31 20:17:14 +0000
---

##### Class Creation

Ruby defines the attributes and behaviors of its objects in classes. You can think of classes as basic outlines of what an object should be made of and what it should be able to do. To define a class, we use syntax similar to defining a method. We replace the def with class and use the CamelCase naming convention to create the name. We then use the reserved word end to finish the definition. Ruby file names should be in snake_case, and reflect the class name. So in the below example, the file name is good_dog.rb and the class name is GoodDog.
```

# good_dog.rb

class GoodDog
end

sparky = GoodDog.new
```

In the above example, we created an instance of our GoodDog class and stored it in the variable sparky. We now have an object. We say that sparky is an object or instance of class GoodDog. This entire workflow of creating a new object or instance from a class is called instantiation, so we can also say that we've instantiated an object called sparky from the class GoodDog. The terminology in OOP is something you'll eventually get used to, but the important fact here is that an object is returned by calling the class method new.

As you can see, defining and creating a new instance of a basic class is simple!
