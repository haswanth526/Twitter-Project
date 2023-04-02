# Twitter-Project
import snscrape.modules.twitter as sntwitter
import pandas as pd
import pymongo
import datetime
import streamlit as st
# (API)
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["twitter_scraping_db"]

p = str(input('Enter the #tag word you want to search: '))
date_entry = input('Enter a date in YYYY-MM-DD format: ')
year, month, day = map(int, date_entry.split('-'))
date1 = datetime.date(year, month, day)
limit_days = int(input('Enter the number of days to limit: '))
tweet_limit = int(input('Enter the tweet limit: '))

end_date = date1
start_date = end_date - datetime.timedelta(limit_days)

tweet_list = []
tweet_list.clear()

def keyword():
    
    query = '{} since:{} until:{}'.format(p, start_date, end_date)
    print(query)
    
    for i, tweet in enumerate(sntwitter.TwitterSearchScraper('{}'.format(query)).get_items()):
        
        if i > tweet_limit:
            break
            
        tweet_list.append({
            'Keyword': '{}'.format(p),
            'Tweet_Date' : tweet.date, 
            'Id': tweet.id, 
            'URL': tweet.url,     
            'content': tweet.content,
            'reply_count': tweet.replyCount,
            'retweet_count': tweet.retweetCount,
            'username': tweet.user.username,
            'like_count': tweet.likeCount})
        
keyword()
df = pd.DataFrame(tweet_list)
df.to_csv('{}.csv'.format(p))

collection_1 = mydb['Collection_1']

df
