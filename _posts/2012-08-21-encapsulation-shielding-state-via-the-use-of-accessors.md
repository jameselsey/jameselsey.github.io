---
title: 'Encapsulation; shielding state via the use of accessors'
date: '2012-08-21T19:41:41+10:00'
permalink: /encapsulation-shielding-state-via-the-use-of-accessors
author: 'James Elsey'
category:
    - Java
tag:
    - best-practice
    - encapsulation
    - java
    - OCPJP
    - oop
    - scjp
---
Encapsulation is a fantastic OOP concept of being able to shield class state via the use of accessor methods.

Imagine that you have a class, perhaps called Person. This class will have some form of state, such as name, age, date of birth. These would most likely be stored against reference variables. If you left these publicly available, then anyone who develops against your class can freely access them and assign them to whatever they feel necessary.

Later on, you may like to change the way some of this state is represented, for example you may wish to start storing ages as Strings instead of Integers, if you do that, then anyone who is dependant on your class will now be screaming up and down as you would have broken their code that interfaces with yours, as you’ve changed the data type and it will cause compilation issues for them.

Encapsulation helps protect against these scenarios, whereby you would make the state private so other people can’t access it directly, but at the same time you would provide public methods so that developers can still use your class, and those public methods would access the private state.

This means that if you change your internal representation, you’d just need to update the implementation of the accessor method to retrieve the new data type, convert it to what the old API was expecting, and return it. Developers using your code won’t be interested in how its stored internally provided they can get their data in and out, if you change something behind the scenes, they won’t care (maybe they would, but this is just an example).

By forcing developers to access state via accessor methods, you can also enforce other restrictions like validation, without encapsulation the developers could put any value into name, and there is nothing you could do about it. However, using accessor methods, you could check the value beforehand and see that it meets your requirements, if it doesn’t, throw an exception.

Encapsulation is generally perceived to be good practice, and you should try to use it where possible. Some languages, such as Groovy even implement this out of the box for you, unless you state otherwise.

Please consider this example from my [SCJP samples](https://github.com/jameselsey/SCJP-OCPJP-SampleCode) Github project, which displays the basics of encapsulation:

```java
package com.jameselsey.demo.scjp.oo_concepts;

/**
 * Author:  JElsey
 * Date:     16/08/2012
 *
 * Demonstrating an understanding of the Encapsulation OOP term, which basically means to shield instance behaviour
 * behind publicly accessible accessors, thus giving more control of the internal representation of the values
 * and provides a single point of access.
 */
public class Encapsulation
{
    public static void main(String[] args)
    {
       // Create a class that doesn't use encapsulation

        UnencapsulatedMess mess = new UnencapsulatedMess();
        mess.personName = "James";
        mess.personAge = 26;
        System.out.println(mess);

        // now create a similar class, but one which implements encapsulation, notice the use of the accessor methods
        // rather than direct variable access
        EncapsulatedDream dream = new EncapsulatedDream();
        dream.setPersonName("James");
        dream.setPersonAge(26);
        System.out.println(dream);


        // Now, lets create another mess, but this time we want to have some control over what values the variable takes
        UnencapsulatedMess anotherMess = new UnencapsulatedMess();
        String someNameThatWouldComeFromAnotherClass = "Daniel";
        int anAgeThatWouldComeFromAnotherClass = 12;
        if (anAgeThatWouldComeFromAnotherClass < 16 || anAgeThatWouldComeFromAnotherClass > 60)
        {
            // handle an error
        }
        else
        {
            anotherMess.personAge = anAgeThatWouldComeFromAnotherClass;
            anotherMess.personName = someNameThatWouldComeFromAnotherClass;
        }
        // Woah! A lot of waffle to validate a value before we place it, and whereever you refer to this unecapsulated
        // class, you'd have to do the same thing, and how easy that would be to get wrong or be inconsistent!

        // With the encapsulated class, you could do all your validation in a central place, and be sure it is ALWAYS invoked,
        // This may not be the BEST place to validate, but its just an example of the benefits of encapsulation.
    }
}


class UnencapsulatedMess
{
    public String personName;
    // no access modifier specified, so takes default
    int personAge;

    @Override
    public String toString()
    {
        return "UnencapsulatedMess{" +
                "personName='" + personName + '\'' +
                ", personAge=" + personAge +
                '}';
    }
}

class EncapsulatedDream
{
    // make the state private, so only accessible within the class itself.
    private String personName;
    private int personAge;

    // Accessors
    public String getPersonName()
    {
        return personName;
    }

    public void setPersonName(String personName)
    {
        this.personName = personName;
    }

    public int getPersonAge()
    {
        return personAge;
    }

    /**
     * Another useful utlitly method that encapsulation aids with, returning the person
     * age as a user friendly, readable String.
     * @return String the persons age as a String
     */
    public String getPersonAgeAsString()
    {
        return personAge + " years old";
    }

    /**
     * This is an accessor method to set the persons age, this method also performs some validation
     * on the value being set
     * @param personAge int the persons age, in years
     */
    public void setPersonAge(int personAge)
    {
        if (personAge < 16 || personAge > 60)
        {
            // Do something here! Throw an exception or log something
        }
        else
        {
            this.personAge = personAge;
        }
    }

    @Override
    public String toString()
    {
        return "EncapsulatedDream{" +
                "personName='" + personName + '\'' +
                ", personAge=" + personAge +
                '}';
    }
}
```