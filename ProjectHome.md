# What #
Simple Test or "Sample" boostrapping library which allows you to instantiate objects (think your domain model) with random sample data.

I created this for two reasons.
I want to "seed" a database with a lot of random Data for testing, but through my JPA layer, not just XML in using DBUnit etc

I also want to initiate an application for "production" with sample data. So the library is not just for Testing, but for Quasi Testing.

# What Data Types do You Support ? #
As this is about generating random data, there are many "types" or sequences of data we need to create.

Sometimes from a fixed list of strings, (Colors : Red, Blue, Green) or sometimes just a sequence or from a MAX MIN range of numbers. Dates, Names and Phone numbers come to mind.

The following is what is currently supported with more to follow and any others as requests come in, or as needed.

## Lists, String etc ##
```
RandomDataGenerator rdg = new RandomDataGenerator()
// create 6 new Positions
Set<Position> positions = rdg.generateSet(6, 
                              new GenConfig().nextFromStringList(
                                               "name"
                                               ,"Guitar, Bass, Drums, Keyboard 1")
                                               ,Position.class);
```

This will set "position.name (getName()) with the "next" in the list of Strings. Guitar first, Bass, Drums etc.

This example asked for 6 Position.class instances. The "labels" from the String list, there is only 4, so the list will repeat back at the beginning for Position instance 5 and 6.
ie:

```
position.getName(); // Guitar
position.getName(); // Bass
position.getName(); // Drums
position.getName(); // Keyboard 1
position.getName(); // Guitar
position.getName(); // Bass
```

## Human Names (Male, Female, Lastname) ##

RandomDataGenerator holds names, such as male and female.

```
 List<Person> randomPersons = rdg.generateList(50,
                new GenConfig()
                 .name(Name.Firstname, "firstname")
                 .name(Name.Lastname, "lastname"), Person.class);

   for (Person p : randomPersons) {
            System.out.println(p);
   }
```

This gives the output like:
```
Hollner, Harry
Helmke, Shay
Staiano, Ernst
Winkelmann, Eldred
Cresci, Kennedy
Hannigan, Glennis
Ypina, Kinley
```

## Date and Time Data ##

For "now()" date
```
rdg.generateList(1, new GenConfig().dateTimeNow("created", DTConfig.RoundToMinute), Person.class);
```
Note in the above example the DTConfig.RoudToMinute (see below for what that is).

Create a random date between two dates.
```
rdg.generateList(10, new GenConfig().dateTimeRandom("created", beginDate, endDate), Person.class);
```
### Date Rounding ###
Rounding is optional, it supports the "chopping" of data for example.

Date is : Fri Sep 16 12:53:11 BST 2011
```
    public static enum DTConfig { 
        RoundToYear,    // Sat Jan 01 00:00:00 GMT 2011
        RoundToMonth,   // Thu Sep 01 00:00:00 BST 2011
        RoundToDay,     // Fri Sep 16 00:00:00 BST 2011
        RoundToHour,    // Fri Sep 16 12:00:00 BST 2011
        RoundToMinute,  // Fri Sep 16 12:53:00 BST 2011
        RoundToSeconds; // Fri Sep 16 12:53:11 BST 2011 - removes milliseconds (not shown)
    }
```

## Lists (A Catch All) ##
Given a list of objects, we can "select" randomly or sequentially from that list.
Technically this case covers all your basic cases that are currently not supported.

Put in the list all the objects you want and we can select from it.
```
 // create a list
 List<String> strings = new ArrayList<String>();
 for (int i = 0; i < 10; i++) {
   strings.add(RandomUtil.randomFromStringSeparatedList("Po,Mo,Go,Bo,Lo,Ho,No,Wo"));
 }

 List<Person> randomPersons = 
     rdg.generateList(50, 
        new GenConfig()
              .nextFromList("lastname", strings)
              .name(Name.MaleFirstname, "firstname"), Person.class);

```

## Nested Generated Objects ##

It supports creating nested objects to a main object.

Imagine a fictitious set of classes, Age and Person.

```
public class Age {
    private String age;
    public String getAge() {
        return age;
    }
    public void setAge(String age) {
        this.age = age;
    }
}

public class Person {

    private String firstname;
    private String lastname;
    private Age myAge;
    private Date created;
    public String getFirstname() {
        return firstname;
    }
    public void setFirstname(String firstname) {
        this.firstname = firstname;
    }
    public String getLastname() {
        return lastname;
    }
    public void setLastname(String lastname) {
        this.lastname = lastname;
    }
    
    public String toString() { 
        return lastname + ", " + firstname;
    }
    public Age getMyAge() {
        return myAge;
    }
    public void setMyAge(Age myAge) {
        this.myAge = myAge;
    }
    public Date getCreated() {
        return created;
    }
    public void setCreated(Date created) {
        this.created = created;
    }

}

```

Then we can create a random Age, on the Person Class. (50 Person classes, with each one a Random Age classes added in).

```
        List<Person> randomPersons = rdg.generateList(50,

                new GenConfig()
                    .name(Name.Firstname, "firstname")
                    .name(Name.Lastname, "lastname")
                    .randomObject(
                             new GenConfig()
                                 .randomFromStringList("age", "eighteen months, eight, thiry three, thirty four")
                             , "myAge"
                             , Age.class
                     )
                 ,Person.class);
```

# Where #
Simply include in your project
(for now download and install)

```

  <repository>
   <id>random-data-generator</id>
   <url>http://random-data-generator.googlecode.com/svn/releases/maven2</url>
   <name>random-data-generator</name>
  </repository>
...
  <dependency>
    <groupId>org.soqqo</groupId>
    <artifactId>random-data-generator</artifactId>
    <version>0.01</version>
    <scope>test</scope>
  </dependency>
```

# Upcoming Supported Data Types #
Next releases will include
  * Number and letter generations (good for passwords and the like)
  * ID Generation
  * Better Human Attribute Support (Male, as Sex can dictates a Male name), Age, Date of Birth (EyeColour, HairColour, Height, Weight, Age, DateOfBirth, Sex, SexMale, SexFemale)
  * Address Information (StreetAddress, CityTown, StateCountyProvince, Country, HouseName, StreetNumber, StreetName, StreetType)
  * Email Addresses (tied to names if names are used ie: john34@foobar.com for John Smith)
  * Phone support