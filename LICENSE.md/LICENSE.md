\##FINAL PROFJECT##
import numpy as np 
import pandas as pd
import random
import re
import datetime
#%%
##DATA TRIMMING STEP 1: making the ratings file smaller by taking random sample from it
##read original file
rating = pd.read_csv('rating.csv')
##total 138493 users, taking random 5000:
r = random.sample(range(1, 138493), 5000)
ratings = rating[rating['userId'].isin(r)]
##New ratings file has 705703 entries
#%%
##Read in other files
movie = pd.read_csv('movie.csv')
gscore = pd.read_csv('genome_scores.csv')
gtags = pd.read_csv('genome_tags.csv')
tag = pd.read_csv('tag.csv')
#%%
##DATA TRIMMING STEP 2: making the gscore file smaller by 
##deleting tags not highly related to each movie
##(define 'highly related' as relevance score > 0.5 )
gscores = gscore[gscore.relevance > 0.5]
#%%
## function to convert tagId to tag name:
def convert_tagId(data):
    result = gtags.tag[gtags.tagId==data].values
    return str(result)
#%%
####################################################################################################################
##Q1:-What are the 10 most commonly seen elements? 
df1=gscores.groupby(['tagId']).count()
top10tagId = list(df1.sort_values(['relevance'])[-10:].index.values)
gtags.tag[gtags.tagId.isin(top10tagId)]
'''
index        tag name (descending order)
301           dialogue
444               good
451    good soundtrack
463              great
467       great ending
645             mentor
741           original
866            runaway
970              story
971       storytelling
'''
#%%
####################################################################################################################
##Q2: What are the average ratings of the movies highly related to these elements?
##mean rating of each movie:
mumovie = ratings.rating.groupby(ratings['movieId']).mean()
for i in top10tagId:
    list1 = list(gscores.movieId[gscores.tagId==i].index)
    print(convert_tagId(i),mumovie[list1].mean())    
'''
['runaway'] 3.6579592707111632
['storytelling'] 3.8801001251564458
['great'] 2.633790497256695
['story'] 3.136835946704368
['good'] 2.762139863337793
['good soundtrack'] 3.453333333333333
['great ending'] 3.75
['dialogue'] 3.612510822510822
['mentor'] 3.336838942307692
['original'] 3.500188927754027
'''
#%%
####################################################################################################################
##Q3:What are the most commonly seen elements in movies of certain genre?
## get a list of all genres:
genres = []
for i in movie.genres.values:
    genres += i.split('|')
genres = set(genres)
## 
for i in genres:    
    list2 = list(movie.movieId[movie.genres.str.contains(i)])
    df2 = gscores[gscores.movieId.isin(list2)].groupby(gscores.tagId).count()
    list3 = list(df2.sort_values('tagId')[-10:].index.values)
    list3 = [convert_tagId(i) for i in list3]
    print(i,list3)
'''
Fantasy ["['special effects']", "['visually appealing']", "['great ending']", "['storytelling']", "['mentor']", "['fantasy world']", "['dialogue']", "['fantasy']", "['story']", "['original']"]
Adventure ["['fun movie']", "['great ending']", "['great']", "['action']", "['good']", "['adventure']", "['mentor']", "['dialogue']", "['story']", "['original']"]
Comedy ["['predictable']", "['great']", "['great ending']", "['good soundtrack']", "['funny']", "['fun movie']", "['dialogue']", "['mentor']", "['comedy']", "['original']"]
IMAX ["['special effects']", "['catastrophe']", "['visually stunning']", "['action']", "['visually appealing']", "['big budget']", "['pg-13']", "['dialogue']", "['story']", "['original']"]
Western ["['vengeance']", "['culture clash']", "['great']", "['harsh']", "['dialogue']", "['mentor']", "['runaway']", "['western']", "['gunfight']", "['original']"]
Film-Noir ["['noir']", "['tense']", "['runaway']", "['cinematography']", "['enigmatic']", "['film noir']", "['dialogue']", "['talky']", "['criterion']", "['original']"]
Mystery ["['police investigation']", "['weird']", "['secrets']", "['storytelling']", "['murder']", "['dialogue']", "['twists & turns']", "['suspense']", "['great ending']", "['original']"]
War ["['brutality']", "['drama']", "['sacrifice']", "['forceful']", "['dialogue']", "['wartime']", "['culture clash']", "['dramatic']", "['war']", "['original']"]
__main__:2: UserWarning: This pattern has match groups. To actually get the groups, use str.extract.
(no genres listed) ["['first contact']", "['franchise']", "['future']", "['good']", "['great']", "['great dialogue']", "['great ending']", "['hospital']", "['visually stunning']", "['war']"]
Musical ["['story']", "['dancing']", "['mentor']", "['fun movie']", "['great']", "['good music']", "['good soundtrack']", "['music']", "['musical']", "['original']"]
Animation ["['good']", "['family']", "['storytelling']", "['computer animation']", "['animals']", "['story']", "['cartoon']", "['animated']", "['animation']", "['original']"]
Crime ["['murder']", "['brutality']", "['violence']", "['good soundtrack']", "['crime']", "['corruption']", "['great ending']", "['mentor']", "['dialogue']", "['original']"]
Romance ["['great ending']", "['good soundtrack']", "['destiny']", "['relationships']", "['dialogue']", "['mentor']", "['love story']", "['romance']", "['romantic']", "['original']"]
Thriller ["['story']", "['murder']", "['chase']", "['twists & turns']", "['good']", "['mentor']", "['dialogue']", "['suspense']", "['great ending']", "['original']"]
Children ["['good']", "['animals']", "['story']", "['childhood']", "['fun movie']", "['kids and family']", "['kids']", "['children']", "['family']", "['original']"]
Sci-Fi ["['catastrophe']", "['story']", "['great ending']", "['dialogue']", "['special effects']", "['science fiction']", "['scifi']", "['sci-fi']", "['sci fi']", "['original']"]
Drama ["['criterion']", "['runaway']", "['story']", "['storytelling']", "['great ending']", "['drama']", "['dialogue']", "['good soundtrack']", "['mentor']", "['original']"]
Documentary ["['corruption']", "['talky']", "['social commentary']", "['narrated']", "['interesting']", "['pornography']", "['criterion']", "['very interesting']", "['documentary']", "['original']"]
Horror ["['cult classic']", "['gory']", "['brutality']", "['weird']", "['scary']", "['creepy']", "['great ending']", "['splatter']", "['horror']", "['original']"]
Action ["['great ending']", "['action packed']", "['good']", "['chase']", "['fight scenes']", "['good action']", "['mentor']", "['dialogue']", "['action']", "['original']"]
'''
#%%
####################################################################################################################
##Q4: What are the most commonly seen elements in movies from certain time period?

##define a function to get the year of the movie from its title:
## 0 stands for unknown (some titles do not have a year)
def get_year(data):
    try:
        out1 = str(re.findall(r'\(\d+\)',data)[0])
        out2 = int(re.findall(r'\d+',out1)[0])
    except:
        out2 = 0
    return out2
movie['Year']=movie.title.apply(get_year)
#%%
## movie after 2000:
list4 = list(movie.movieId[movie.Year >= 2000].values)
df3 = gscores[gscores.movieId.isin(list4)].groupby(gscores.tagId).count()
list5 = list(df3.sort_values('tagId')[-10:].index.values)
list5 = [convert_tagId(i) for i in list5]
print('After 2000: ', list5)
'''
After 2000:  ["['predictable']", "['good']", "['pg-13']", "['storytelling']", "['story']", "['good soundtrack']", "['great ending']", "['dialogue']", "['mentor']", "['original']"]
'''
#%%
## movie in 1990s:
list4 = list(movie.movieId[(2000 >= movie.Year)&(movie.Year >= 1990)].values)
df3 = gscores[gscores.movieId.isin(list4)].groupby(gscores.tagId).count()
list5 = list(df3.sort_values('tagId')[-10:].index.values)
list5 = [convert_tagId(i) for i in list5]
print('1990s: ', list5)
'''
1990s:  ["['storytelling']", "['story']", "['predictable']", "['good']", "['great']", "['good soundtrack']", "['great ending']", "['dialogue']", "['mentor']", "['original']"]
'''
#%%
## movie in 1980s:
list4 = list(movie.movieId[(1990 >= movie.Year)&(movie.Year >= 1980)].values)
df3 = gscores[gscores.movieId.isin(list4)].groupby(gscores.tagId).count()
list5 = list(df3.sort_values('tagId')[-10:].index.values)
list5 = [convert_tagId(i) for i in list5]
print('1980s: ', list5)
'''
1980s:  ["['brutality']", "['80s']", "['fun movie']", "['great']", "['good']", "['good soundtrack']", "['dialogue']", "['great ending']", "['mentor']", "['original']"]
'''
#%%
## movie in 1970s:
list4 = list(movie.movieId[(1980 >= movie.Year)&(movie.Year >= 1970)].values)
df3 = gscores[gscores.movieId.isin(list4)].groupby(gscores.tagId).count()
list5 = list(df3.sort_values('tagId')[-10:].index.values)
list5 = [convert_tagId(i) for i in list5]
print('1970s: ', list5)
'''
1970s:  ["['brutality']", "['great']", "['talky']", "['good soundtrack']", "['criterion']", "['great ending']", "['runaway']", "['mentor']", "['dialogue']", "['original']"]
'''
#%%
## movie in 1960s:
list4 = list(movie.movieId[(1970 >= movie.Year)&(movie.Year >= 1960)].values)
df3 = gscores[gscores.movieId.isin(list4)].groupby(gscores.tagId).count()
list5 = list(df3.sort_values('tagId')[-10:].index.values)
list5 = [convert_tagId(i) for i in list5]
print('1960s: ', list5)
'''
1960s:  ["['oscar (best directing)']", "['cinematography']", "['great']", "['great ending']", "['mentor']", "['talky']", "['dialogue']", "['criterion']", "['runaway']", "['original']"]
'''
#%%
## movie before 1960:
list4 = list(movie.movieId[(1960 >= movie.Year)&(movie.Year >= 1)].values)
df3 = gscores[gscores.movieId.isin(list4)].groupby(gscores.tagId).count()
list5 = list(df3.sort_values('tagId')[-10:].index.values)
list5 = [convert_tagId(i) for i in list5]
print('Before 1960: ', list5)
'''
Before 1960:  ["['imdb top 250']", "['dialogue']", "['great']", "['oscar (best directing)']", "['oscar (best actress)']", "['classic']", "['criterion']", "['talky']", "['runaway']", "['original']"]
'''
#%%
####################################################################################################################
##Q5: What are the most commonly seen elements in top-rated movies? Bottom-rated movies?
## Top rated movies (top 200):
top = list(mumovie.sort_values()[-200:].index.values)
## Bottom rated movies (bottom 200):
bottom = list(mumovie.sort_values()[:200].index.values)
#%%
df3 = gscores[gscores.movieId.isin(top)].groupby(gscores.tagId).count()
list5 = list(df3.sort_values('tagId')[-10:].index.values)
list5 = [convert_tagId(i) for i in list5]
print('Top', list5)
'''
Top ["['brutality']", "['pornography']", "['harsh']", "['mentor']", "['talky']", "['runaway']", "['good soundtrack']", "['melancholic']", "['criterion']", "['original']"]
'''
#%%
df3 = gscores[gscores.movieId.isin(bottom)].groupby(gscores.tagId).count()
list5 = list(df3.sort_values('tagId')[-10:].index.values)
list5 = [convert_tagId(i) for i in list5]
print('Bottom', list5)
'''
Bottom ["['franchise']", "['stupid as hell']", "['horrible']", "['family']", "['pointless']", "['bad plot']", "['mentor']", '["so bad it\'s funny"]', "['predictable']", "['original']"]
'''
#%%
####################################################################################################################
##Q6: Correlation between the number of tags a particular reviewer assigned and his/her average ratings.
##top 1000 users by total tags assigned
top1000 = list(tag.groupby('userId').count().sort_values('tag')[-1000:].index.values)
##mean ratings of each user
murating_user = ratings.groupby('userId').mean().drop('movieId',1)
murating_user[murating_user.index.isin(top1000)]
murating_user['userId'] = murating_user.index.values
##intersection of this two groups, merge into a new dataframe, df5
df4 = pd.DataFrame()
df4['userId'] = list(tag.groupby('userId').count().sort_values('tag')[-5000:].index.values)
df4['tagtotal'] = list(tag.groupby('userId').count().sort_values('tag')[-5000:].tag.values)
df5=pd.merge(df4,murating_user)
df5
##in df5,userId is user`s id, tagtotal is the number of tags assigned by that user, rating is the mean rating of that user
##plots can be plotted between tagtotal and rating.
#%%
####################################################################################################################
##Q7: How do the trends in popular tags and elements change over time?
## This question analyzes popularity of user assigned tags (not genome tags) and its relation to time of the tag was assigned, not time of the movie.
## making timestamp column a datetime object
format = "%Y-%m-%d %H:%M:%S"
def get_time(data):
    result = datetime.datetime.strptime(data,format)
    return result
tag['time']  = tag.timestamp.apply(get_time)

##get the time range:
tag.time.min()
tag.time.max()
##earliest data from '2005-12-24 13:00:10', latest from '2015-03-31 03:09:12'
## Dividing the data into two groups: before and after '2010-01-01 00:00:00' (group 0 and group 1):
def grouping(data):
    if data > datetime.datetime.strptime('2015-01-01 00:00:00',format):
        return 2015
    elif data > datetime.datetime.strptime('2014-01-01 00:00:00',format):
        return 2014
    elif data > datetime.datetime.strptime('2013-01-01 00:00:00',format):
        return 2013
    elif data > datetime.datetime.strptime('2012-01-01 00:00:00',format):
        return 2012
    elif data > datetime.datetime.strptime('2011-01-01 00:00:00',format):
        return 2011
    elif data > datetime.datetime.strptime('2010-01-01 00:00:00',format):
        return 2010
    elif data > datetime.datetime.strptime('2009-01-01 00:00:00',format):
        return 2009
    elif data > datetime.datetime.strptime('2008-01-01 00:00:00',format):
        return 2008
    elif data > datetime.datetime.strptime('2007-01-01 00:00:00',format):
        return 2007
    elif data > datetime.datetime.strptime('2006-01-01 00:00:00',format):
        return 2006
    elif data > datetime.datetime.strptime('2005-01-01 00:00:00',format):
        return 2005
tag['group']=tag.time.apply(grouping)
#%%
df6 = pd.DataFrame()
df6['rank'] = list(reversed(range(21)[1:]))
for i in set(tag.group.values):
    df6[i] = list(tag[tag['group']==i].groupby('tag').count().sort_values('movieId')[-20:].index.values)
df6
##df6 contains top 20 tags for each year from 2005 to 2015
#%%
'''
     rank             2005                      2006                2007  \
0     20           kitsch                    comedy           satirical   
1     19     kevin spacey                      70mm             AFI 100   
2     18   jack nicholson              movie to see             classic   
3     17            harsh                comic book     based on a book   
4     16  stanley kubrick                    sci-fi        World War II   
5     15      guy ritchie               Eric's Dvds           ClearPlay   
6     14           family                     anime           DVD-Video   
7     13         eighties          In Netflix queue              quirky   
8     12    edward norton                       own          disturbing   
9     11            drugs  Nudity (Topless - Brief)            humorous   
10    10    coen brothers                    action              murder   
11     9        christian          Nudity (Topless)               tense   
12     8     bruce willis                       dvd  seen at the cinema   
13     7          british                    Disney            stylized   
14     6           brazil            Can't remember             Betamax   
15     5         Terrible             erlend's DVDs   adapted from:book   
16     4     fuckoff nazi                Bibliothek         atmospheric   
17     3             twee       seen more than once                   R   
18     2        overrated      Oscar (Best Picture)        imdb top 250   
19     1        brad pitt                   classic        Tumey's DVDs   

                                           2008             2009  \
0                                        on dvr           satire   
1   easily confused with other movie(s) (title)           aliens   
2                                     superhero          fantasy   
3                                        comedy      time travel   
4                                        sci-fi          classic   
5                            Sven's to see list         stylized   
6                                     ClearPlay     imdb top 250   
7                                       Below R         dystopia   
8                                  movie to see       true story   
9                              Nudity (Topless)           quirky   
10                            directorial debut        DVD-Video   
11                                        owned      dark comedy   
12                                      library          surreal   
13                                            R     twist ending   
14                                    Criterion      atmospheric   
15                                 Tumey's DVDs           action   
16                                       To See           comedy   
17                       National Film Registry           sci-fi   
18                              based on a book          Betamax   
19                        less than 300 ratings  based on a book   

                2010               2011                2012  \
0         disturbing            classic         predictable   
1           violence              drugs   social commentary   
2             aliens  thought-provoking          psychology   
3            fantasy  social commentary   thought-provoking   
4            romance            romance            stylized   
5            classic           dystopia  visually appealing   
6   post-apocalyptic           stylized         dark comedy   
7              funny            fantasy              quirky   
8             quirky               BD-R            dystopia   
9           dystopia        dark comedy           Criterion   
10          stylized             quirky             surreal   
11        psychology         psychology        twist ending   
12            comedy             action              comedy   
13       dark comedy       twist ending               funny   
14            action              funny          might like   
15      twist ending    based on a book              action   
16       atmospheric            surreal     based on a book   
17           surreal        atmospheric              sci-fi   
18   based on a book             comedy         atmospheric   
19            sci-fi             sci-fi                BD-R   

                  2013                   2014                2015  
0               horror                  funny                dark  
1             politics                 quirky     based on a book  
2             dystopia                 comedy             fantasy  
3             original             psychology             romance  
4         twist ending            NO_FA_GANES              quirky  
5              violent                 action         time travel  
6        noir thriller      social commentary   thought-provoking  
7                tense               dystopia         dark comedy  
8             thriller      thought-provoking               funny  
9            franchise                surreal        twist ending  
10                dark                    CLV  visually appealing  
11               funny     visually appealing            stylized  
12             surreal        based on a book            dystopia  
13     based on a book               reviewed             surreal  
14  visually appealing           twist ending              comedy  
15              sci-fi            atmospheric                 CLV  
16         atmospheric                 sci-fi              action  
17              comedy  nudity (full frontal)         atmospheric  
18              action                   BD-R                BD-R  
19                BD-R       nudity (topless)              sci-fi  
>>> 
'''

