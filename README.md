# How to Get More Comments on Hacker News #

Hacker News is a popular news website where people can upload posts and comment about various topics related to the tech field. We will be focusing on two types of posts made on Hacker News: Ask HN and Show HN posts. Ask HN posts are posts that are made for the purpose of asking a question to the community. Show HN posts are posts that are meant to showcase a particular project, product, an insight, or just anything interesting in general. 

The dataset that will be used in this study contains data of approximately 20,000 randomly selected Hacker News posts that received comments. 

The two questions we will be addressing in this project are: 
1. Do Ask HN or Show HN receive more comments on average? 
2. Do posts created at certain time of the day receive more comments on average?

Ultimately, we want to find out which hours of the day we should make a Hacker News post that will receive the moust amount of  comments. 



**Opening the Data and Removing Headers**

We will begin by opening and reading our dataset into a list of lists. 


```python
import csv
opened_file = open('hacker_news.csv')
hn = list(csv.reader(opened_file))
hn[:5]
```




    [['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at'],
     ['12224879',
      'Interactive Dynamic Video',
      'http://www.interactivedynamicvideo.com/',
      '386',
      '52',
      'ne0phyte',
      '8/4/2016 11:52'],
     ['10975351',
      'How to Use Open Source and Shut the Fuck Up at the Same Time',
      'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/',
      '39',
      '10',
      'josep2',
      '1/26/2016 19:30'],
     ['11964716',
      "Florida DJs May Face Felony for April Fools' Water Joke",
      'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/',
      '2',
      '1',
      'vezycash',
      '6/23/2016 22:20'],
     ['11919867',
      'Technology ventures: From Idea to Enterprise',
      'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429',
      '3',
      '1',
      'hswarna',
      '6/17/2016 0:01']]



Upon examining the first 5 rows of the data, we can see that the first row is composed of the header labels. In order to process the data without causing errors, we need to isolate this header row and remove it. 


```python
headers = hn[0]
hn = hn[1:]
print(headers)
print(hn[:5])
```

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']
    [['12224879', 'Interactive Dynamic Video', 'http://www.interactivedynamicvideo.com/', '386', '52', 'ne0phyte', '8/4/2016 11:52'], ['10975351', 'How to Use Open Source and Shut the Fuck Up at the Same Time', 'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/', '39', '10', 'josep2', '1/26/2016 19:30'], ['11964716', "Florida DJs May Face Felony for April Fools' Water Joke", 'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/', '2', '1', 'vezycash', '6/23/2016 22:20'], ['11919867', 'Technology ventures: From Idea to Enterprise', 'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429', '3', '1', 'hswarna', '6/17/2016 0:01'], ['10301696', 'Note by Note: The Making of Steinway L1037 (2007)', 'http://www.nytimes.com/2007/11/07/movies/07stein.html?_r=0', '8', '2', 'walterbell', '9/30/2015 4:12']]
    

Upon printing the first 5 rows again, we can see that our hn dataset no longer contains the header row. 

**Extracting Ask HN and Show HN Posts**

Since we are concerned with only Ask HN and Show HN posts, we will filter through the dataset and separate these two types of posts into each of their respective lists. 


```python
ask_posts = []
show_posts = []
other_posts = []

for row in hn: 
    title = row[1]
    if title.lower().startswith("ask hn"):
        ask_posts.append(row)
    elif title.lower().startswith("show hn"):
        show_posts.append(row)
    else:
        other_posts.append(row)
        
print(len(ask_posts))
print(len(show_posts))
print(len(other_posts))
```

    1744
    1162
    17194
    

There are 1744 ask posts, and 1162 show posts. 

**Calculating the Average Number of Comments**

The Ask HN and Show HN posts have been separated, and we wil now calculate the average number of comments per each type of post. 


```python
# ask comments
total_ask_comments = 0

for post in ask_posts:
    total_ask_comments += int(post[4])
    
avg_ask_comments = total_ask_comments / len(ask_posts)
print(avg_ask_comments)

# show comments
total_show_comments = 0

for post in show_posts: 
    total_show_comments += int(post[4])
    
avg_show_comments = total_show_comments / len(show_posts)
print(avg_show_comments)
```

    14.038417431192661
    10.31669535283993
    

We can see that ask posts receive about 14 comments per post, while show posts receive 10. This answers our first question of this research; on average, ask posts receive more comments than show posts on Hacker News. Next, we will evaluate the exact hours of the day that posts are made and analyze when they receive the most comments. For the remainder of this analysis, we will focus specifically on ask posts. 

**Total Number of Posts and Comments By Hour**

Here, we compute the amount of ask posts that are made by each hour of the day, along with the total number of comments those posts received.


```python
import datetime as dt

result_list = []

for post in ask_posts:
    result_list.append((post[6], int(post[4])))
    
counts_by_hour = {}
comments_by_hour = {}

for row in result_list:
    date = row[0]
    comment = row[1]
    hour = dt.datetime.strptime(date, "%m/%d/%Y %H:%M").strftime("%H")
    if hour not in counts_by_hour:
        counts_by_hour[hour] = 1
        comments_by_hour[hour] = comment
    else:
        counts_by_hour[hour] += 1
        comments_by_hour[hour] += comment
    
comments_by_hour
```




    {'09': 251,
     '13': 1253,
     '10': 793,
     '14': 1416,
     '16': 1814,
     '23': 543,
     '12': 687,
     '17': 1146,
     '15': 4477,
     '21': 1745,
     '20': 1722,
     '02': 1381,
     '18': 1439,
     '03': 421,
     '05': 464,
     '19': 1188,
     '01': 683,
     '22': 479,
     '08': 492,
     '04': 337,
     '00': 447,
     '06': 397,
     '07': 267,
     '11': 641}



**Average Number of Comments By Hour**

We will now calculate the average number of comments an ask post receives by the hour. 


```python
avg_by_hour = []

for hr in comments_by_hour:
    avg_by_hour.append([hr, comments_by_hour[hr] / counts_by_hour[hr]])
    
avg_by_hour
```




    [['09', 5.5777777777777775],
     ['13', 14.741176470588234],
     ['10', 13.440677966101696],
     ['14', 13.233644859813085],
     ['16', 16.796296296296298],
     ['23', 7.985294117647059],
     ['12', 9.41095890410959],
     ['17', 11.46],
     ['15', 38.5948275862069],
     ['21', 16.009174311926607],
     ['20', 21.525],
     ['02', 23.810344827586206],
     ['18', 13.20183486238532],
     ['03', 7.796296296296297],
     ['05', 10.08695652173913],
     ['19', 10.8],
     ['01', 11.383333333333333],
     ['22', 6.746478873239437],
     ['08', 10.25],
     ['04', 7.170212765957447],
     ['00', 8.127272727272727],
     ['06', 9.022727272727273],
     ['07', 7.852941176470588],
     ['11', 11.051724137931034]]



**Sorting the Data**

Now that we have found the average number of comments a post receives each hour, we have all the information that we need. The final step is to sort and organize this information in a way that is easier to read. The following code will display top 5 highest average number of comments in descending order. 


```python
swap_avg_by_hour = []

for row in avg_by_hour:
    swap_avg_by_hour.append([row[1], row[0]])
print(swap_avg_by_hour)

sorted_swap = sorted(swap_avg_by_hour, reverse = True)
sorted_swap 

print("")

print("Top 5 Hours for Ask Posts Comments")

for avg, hr in sorted_swap[:5]:
    print(
    "{}: {:.2f} average comments per post".format(
        dt.datetime.strptime(hr, "%H").strftime("%H:%M"),avg)
    )
```

    [[5.5777777777777775, '09'], [14.741176470588234, '13'], [13.440677966101696, '10'], [13.233644859813085, '14'], [16.796296296296298, '16'], [7.985294117647059, '23'], [9.41095890410959, '12'], [11.46, '17'], [38.5948275862069, '15'], [16.009174311926607, '21'], [21.525, '20'], [23.810344827586206, '02'], [13.20183486238532, '18'], [7.796296296296297, '03'], [10.08695652173913, '05'], [10.8, '19'], [11.383333333333333, '01'], [6.746478873239437, '22'], [10.25, '08'], [7.170212765957447, '04'], [8.127272727272727, '00'], [9.022727272727273, '06'], [7.852941176470588, '07'], [11.051724137931034, '11']]
    
    Top 5 Hours for Ask Posts Comments
    15:00: 38.59 average comments per post
    02:00: 23.81 average comments per post
    20:00: 21.52 average comments per post
    16:00: 16.80 average comments per post
    21:00: 16.01 average comments per post
    

**Conclusion**

Upon analyzing posts and comments on Hacker News, we found the exact hour of the day in which a post receives the most amount of comments. The dataset we analyzed gave us the hours in the Eastern Time Zone. On average, Hacker News posts tend to receive the most ammount of comments at 3PM ET (7PM Pacific). At 38.59 comments per post, this is the prime time when users are the most active. If we were to set about making a post on Hacker News, 3PM ET seems to be the golden hour that would position our post to be seen by the most amount of people.

It's always nice to find a clear first place when it comes to data analysis. 2AM ET comes at second place at 23.81 average comments per post, which is a significant difference of almost 15 comments. It makes sense that users are the most active around 3PM ET. People are generally active and have the most free time around this time. 

However, there are a couple things to keep in mind. We worked with a sample dataset that is a representation of the whole data. There is always the chance that the findings may be a little different if we worked with a bigger dataset. In addition, there are other reasons why a post receives a lot of comments other than when it was posted. Content is important and simply posting at 3PM will not guarantee us a lot of comments. In future research, one thing to consider is working with a bigger dataset and finding what kind of topics receive the most amount of comments. 

In the end, data analysis provided us with important insights on how to receive more comments on Hacker News. 
