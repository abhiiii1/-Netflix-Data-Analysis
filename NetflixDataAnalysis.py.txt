import numpy as np # linear algebra
import pandas as pd # for data preparation
import plotly.express as px # for data visualization
from textblob import TextBlob # for sentiment analysis

df=pd.read_csv('netflix_titles.csv') # Reading CSV file and Creating DataFrame
print(df.shape) # To fetch Dimension of Dataframe
print(df.columns) # To print label of each columns

# Requirement-1 : To Analyze the distribution of content ratings on Netflix and display it as Pie Chart
x = df.groupby(['rating']).size().reset_index(name='counts')
img1 = px.pie(x, values='counts', names='rating',title='Distribution of Content Ratings on Netflix',
                  color_discrete_sequence=px.colors.qualitative.Set3)
img1.show()                         

# Requirement-2 : To Analyze the top 5 successful directors on this platform and display as bar chart.
df['director']=df['director'].fillna('No Director Specified')
filt_dir=pd.DataFrame()
filt_dir=df['director'].str.split(',',expand=True).stack()
filt_dir=filt_dir.to_frame()
filt_dir.columns=['Director']
dir=filt_dir.groupby(['Director']).size().reset_index(name='Total Content')
dir=dir[dir.Director !='No Director Specified']
dir=dir.sort_values(by=['Total Content'],ascending=False)
dirTop5=dir.head()
dirTop5=dirTop5.sort_values(by=['Total Content'])
img2=px.bar(dirTop5,x='Total Content',y='Director',title='Top 5 Directors on Netflix')
img2.show()

# Requirement-3 : To Analyze the top 5 successful actors on this platform and display as bar chart.
df['cast']=df['cast'].fillna('No Cast Specified')
filt_cast=pd.DataFrame()
filt_cast=df['cast'].str.split(',',expand=True).stack()
filt_cast=filt_cast.to_frame()
filt_cast.columns=['Actor']
actors=filt_cast.groupby(['Actor']).size().reset_index(name='Total Content')
actors=actors[actors.Actor !='No Cast Specified']
actors=actors.sort_values(by=['Total Content'],ascending=False)
actorsTop5=actors.head()
actorsTop5=actorsTop5.sort_values(by=['Total Content'])
img3=px.bar(actorsTop5,x='Total Content',y='Actor', title='Top 5 Actors on Netflix')
img3.show()

# Requirement-4 : To Analyze the trend of production over the years on Netflix and display it as line chart.
df1=df[['type','release_year']]
df1=df1.rename(columns={"release_year": "Release Year"})
df2=df1.groupby(['Release Year','type']).size().reset_index(name='Total Content')
df2=df2[df2['Release Year']>=2010]
img4 = px.line(df2, x="Release Year", y="Total Content", color='type',title='Trend of content produced over the years on Netflix')
img4.show()

# Requirement-5 : To Analyze the sentiment of content on Netflix and display as bar chart.
dfx=df[['release_year','description']]
dfx=dfx.rename(columns={'release_year':'Release Year'})
for index,row in dfx.iterrows():
    z=row['description']
    testimonial=TextBlob(z)
    p=testimonial.sentiment.polarity
    if p==0:
        sent='Neutral'
    elif p>0:
        sent='Positive'
    else:
        sent='Negative'
    dfx.loc[[index,2],'Sentiment']=sent
dfx=dfx.groupby(['Release Year','Sentiment']).size().reset_index(name='Total Content')
dfx=dfx[dfx['Release Year']>=2010]
img5 = px.bar(dfx, x="Release Year", y="Total Content", color="Sentiment", title="Sentiment of content on Netflix")
img5.show()