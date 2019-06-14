I recently competed in the [Haha 2019 Competition](https://competitions.codalab.org/competitions/22194) and ended up getting second place on predicting whether a tweet would be funny or not and third place on predicting how funny the tweet would be.  

![clas_ranking](/assets/images/2019-06-11-Haha-2019-Lessons-Learned/clas_ranking.png)

I wanted to share some of my lessons learned from this competition to hopefully prevent somebody else (maybe even my future self) from making the same mistakes.  

1. **Using `git add .` to put my code into a Github Repo**

   `git add .` is the only way that I have ever added files to a project and I learned that it is a terrible way to add files to a Github repo.  Basically this says to add every file in your current directory recursively into your Github repo.  I'm sure if you know what you are doing, this isn't an issue, but I did not.  This was my attempt to put a starting repo into Github and then I was going to figure out what I actually wanted up there and take the rest down.  What ended up happening instead was I added all of the garbage files and every big file into my repo.  A better approach would have been to start small and only add the files that I definitely wanted added.  This for me would have been the .ipynb files and not a whole lot else.

2. **Creating a new databunch every time I reran the notebook**

   One mistake that I made that became more obvious as the competition got closer to completion was creating a new databunch every time I ran the notebook.  The problem with doing this is that not only does it waste time, it also changes up the validation set every time.  This is great when you are wanting to make sure that your model is generalizable, but it is less ideal when trying to test whether a change is actually helpful and your score goes up slightly when looking at your validation scores.  Saving the databunch will help you maintain your sanity when trying slight tweaks and improvements.  

3. **Not saving things required for reproducibility**

   This ended up being one of my less impactful issues, but it could have been a lot bigger deal.  I didn't realize that not only did I need to save the model to reload the model, I also needed to save the vocab associated with the databunch.  I put it lower than the databunch mistake because if I would have done that, it would have made this less important. 

   ```python
   #Load vocab in fastai
   sp_vocab = Vocab.load("30k_Haha.vocab")
   
   #Save vocab in fastai
   sp_vocab.save("30k_Haha.vocab")
   ```

4. **Not keeping the Github Repo clean**

   This may seem similar to #1 and it is similar, but it is a different issue.  Where did my submissions go? Where did my notebooks live?  Where did my test notebooks go?  All of these were in my root directory.  This isn't an issue when you have one notebook and one dataset, but it will grow.  Just set your structure up at the beginning and have a directory for test notebooks, solid notebooks, data, models, and submissions.  

5. **Not Auto-dating Everything that saves**

   When I saved things, I manually changed the names of everything.  This led to some files being overwritten that I would have liked to have had.  I also would have used this in #4 to not only put them in the directories I mentioned, but also add a date file to those folders so I could have more organization.  

   I would do something like this next time and run it in the first cell of the notebook so that all of the cells from that run would be timestamped together.  

   ```python
   import datetime
   year, month, day, hour, minute, second,_,_,_ = datetime.datetime.now().timetuple()
   ```

6. **Not Trying Blah**

   One quote that I first heard from Jeremy Howard during his fastai deep learning course is that if you are wondering if blah is a good idea, to try blah and see.  Until you test out an idea it could be both the best and worst idea you've ever had and you won't know which it is until you test it out.  

 7. **Not changing hyper-parameters**

    It took me way too long to start modifying the hyper-parameters when working on this problem.  Change everything and see how it helps or hurts things!

Thanks for reading and hopefully there is something useful here for you.  My idea is to do a recap like this after each competition and hopefully over time it makes me a better coder and practitioner.  My next blog post is going to be a walkthrough of Hiromi and my approach to this competition because even with all of the mistakes I made, we ended up with a pretty solid classifier and regressor at the end of the day.  







