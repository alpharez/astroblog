---
description: For every work task, I create a markdown file with a frontmatter header
  that I tag with the technologies that I am working with in the task. I wrote a python
  script to crawl all of the files and then show a tag cloud with all of the technologies
  I have been working on in the past few years.
pubDate: '2024-03-22'
title: What I Have Been Working On
---


For every work task, I create a markdown file with a frontmatter header that I tag with the technologies that I am working with in the task.  I wrote a python script to crawl all of the files and then show a tag cloud with all of the technologies I have been working on in the past few years.

```
# Mine Data from markdown files

# ---
# type: chg
# id:
# date:
# customer:
# contact:
# email:
# phone:
# siteID:
# tags: ["#asr", "#bgp"]
# ---

import os
#import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import frontmatter
from wordcloud import WordCloud
from wordcloud import STOPWORDS
from wordcloud import ImageColorGenerator

directory = "/Users/clemens/Documents/mops/"
output_file = directory + "2-index.md"
#directory = "C:\\Users\\steve\\Documents\\mops\\"
count_files = 0
tagstr = ""
dataset = []

#outfile = open(output_file, "w")

if __name__ == '__main__':
    
    for root, dirs, files in os.walk(directory):
        for file in files:
            tagstr = ""
            if file.endswith(".md"):
                count_files = count_files + 1
                filepath = os.path.join(root, file)
                #print(filepath)
                try:
                    data = frontmatter.load(filepath, encoding="utf-8", handler=None)
                    # print(f"{data['id']} {data['customer']} {data['contact']} {data['email']} {data['tags']}")
                    
                    for tag in data['tags']:
                        if tag is not None:
                            dataset.append(tag)
                                        
                except UnicodeDecodeError:
                    print(f"Error at {filepath} (UnicodeDecodeError)")
                except KeyError:
                    # print(f"Error at {filepath} (KeyError)")
                    pass

df = pd.DataFrame(data = dataset, columns=['Tag'])
text = " ".join(i for i in df.Tag)
stopwords = set(STOPWORDS)
wordcloud = WordCloud(stopwords=stopwords, collocations=False, background_color="white").generate(text)

plt.figure(figsize=(15,10))
plt.imshow(wordcloud, interpolation='bilinear')
plt.axis("off")
plt.show()
```

![tagcloud](./tagcloud2.png)

This is what I've been working on.
