---
layout: post
title:      "Cleaning & Exploring Dataframes == Meeting A Person for the First Time"
date:       2020-07-31 18:13:45 -0400
permalink:  mod_2_project_blog
---

As a relatively new python learner (almost 4 months), I've found that the way I treat a new dataframe is a lot like how I treat people I've just met... to a certain extent. As I'm importing all my necessary files, it's like I see the person across the room. I can't read too much into them, after all looks aren't everything - and neither is a csv filename. Once I've gone through the process of naming the variable that will become my dataframe, it becomes an exploratory phase; Finding all the "faults", reasons, and values this person (dataframe) holds close. 

this_person = pd.read_csv('home_prices.csv')

I begin to dig into this_person. I want to find out what makes them tick, what kind of values this_person considers important. Of course I'll be respectful, I don't want to push my values onto someone I've met (just yet) - I'd rather hear them talk first about what activities (columns) they like, and what types of activities they are. So I'll get all my overview information within the first few minutes:

this_person.head()

this_person.describe()

this_person.info()

print(len(this_person))

After getting a rough feel for this_person's column types, I begin to slowly (and gently) question why they see some ideas (columns) a certain way (a type), and maybe look at it from a different perspective.

For example:
this_person sees 'date' as a string object - which isn't too crazy, but they could make it easier on themselves (and me). So I suggest to them, "Hey, you know I used to see 'date' as a string object too, but there's an even cooler and easier way to see it - as a pd.datetime object!" They almost always agree, and so I help this_person change their 'date' column into a pd.datetime object. Easy.

this_person['date'] = pd.to_datetime(this_person['date'])

It's also important to see if this_person contains any null values for any columns, or any weird, out-of-the-ordinary values. So I make a quick function that isn't too intrusive and can find any obvious null values:

def perc_null(df):
    """
    Definition:
    Returns percentage of null values within each column of a dataframe
    
    Args:
    df = selected dataframe
    
    Returns:
    each column name of the df along with the percentage of null values in each column
    """
    return df.isna().sum().divide(len(df))*100
		
Just like that, I can now see that a few of the columns for this_person contain null values. The next step is to figure out what to replace these null values with. When dealing with null values, I have to consider what type the values are within the column (are they categorical, numerical, ordinal?) and then figure out the best way to deal with them. There are 3 foundational strategies for dealing with null values:

1. Removing the null values - Probably the easiest way to get rid of null values, but at what cost? Well it depends on the percentage of null values. if there are over 10% null values within a single column and I decide to toss out all rows containing null values in that column, I lose 10% of my dataframe, which can make things unacceptable depending on the size of the dataframe. 

2. Replacing the null values - I could replace the null values with the most common value, column.mean() or column.median(), or if it's a categorical variable, the value at the top of column.value_counts(). Also if it's categorical, I could merely replace the null values with something like 'missing' or 'N/A'. The downside to doing this is that it could create a lot of noise in my dataframe and possibly significantly alter my findings.

3. Keeping the null values - If I feel that having a null value could be important, then I could definitely keep it. The only problem is that if it's a numerical column, later on down the road when I begin modeling, I could run into some problems.

For me, it always depends. Using intuition is always a solid way to go, rather than just putting your head down and saying "if it falls within these parameters I'm always going to do it this way." Just like people, every DataFrame is different. You have to let them talk and show you what they mean. They won't always be right, which is why it's up to you to clean up their act and maybe suggest a better option to go about something. 

For example:
this_person sees sqft_basement as a string object, which is clearly incorrect. However, if I were to suggest changing that column into an integer, I would most likely get pushback (an error) from this_person. Upon closer examination, we see that there are also string values('?') within that column. So what should I do? change all those values with '?' into a string '0' and then change the type of the column to an integer? Should I drop all rows with '?' in their sqft_basement cell? Nope.

Instead, I use my brain and figure out that there is a column called sqft_above and sqft_living. Upon closer examination of what these columns mean, sqft_above is the amount of sqft area the house has not including the basement area. While sqft_living includes everything. So I simply overwrite this_person's ideas of sqft_basement with a simple calculation:

this_person['sqft_basement'] = this_person['sqft_living'] - this_person['sqft_above']

Voila, problem solved. It turns out that this_person had the answer in them all along; I didn't have to argue with them or dive into a complex argument when it could just be solved by showing them facts they knew, and putting it together for them. As time goes on, I get to know this_person's values and columns, slowly changing them into what I think would make them best for making a good model. 

Just like meeting a person, it's a good idea to not rush getting to know them and taking the time to really show that you care. How often do you stumble across a person who is just pristine and is the best person ever? Probably very rarely. The same can be said for a dataframe; most dataframes do not come in all clean and ready to work, it's never that simple. That would be like me sqiping right on someone I met on tinder without really talking to them and after having our first date, asking them to marry me. The risk of me missing something I consider valuable and important for someone to have may not be in that person. For example, I believe that school is important and discipline is a major pillar in the foundation of the pursuit of knowledge, and having that drive, pursuit of knowledge, is something important to me. They may have an entirely different view on that topic, which could be problematic if neither of us wanted to changes our views and we raised a child together. 

Just like quality relationships, clean dataframes are not easy to come by, and while learning what to look for may seem easy, showing you care about their columns (while sometimes not the best), the types of values within those columns, taking the time to get to know them, and really delving into those deep areas will help you develop a clean, high quality dataframe.  



