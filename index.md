# Final code:
```python
import pandas as pd
import csv
from csv import writer
import operator


# Importing data
olderBuddyData = pd.read_csv('inputCSVs/Advisor1-Advisor2-cohort-older.csv')
youngerBuddyData = pd.read_csv('inputCSVs/Advisor1-Advisor2-cohort-younger.csv')


# Initialize the csv, *it will write over the csv*
with open('Advisor1-Advisor2-results-unsorted.csv', 'w', newline='') as file:
    writerHeader = writer(file)
    

# Going through to test every possible combination of buddies
for indexYounger, person in youngerBuddyData.iterrows():
    # This is just to store the name to make result output easier
    personName = person['Name']

    #personAdvisor = person['Advisory']
   
    # For each question you will need to do this for the *younger buddies*:
    # Access the person's response
    personMakerspaces = person['Makerspace']
    # Make a list from the person's response
    personMakerspacesList = personMakerspaces.split(',')
    # Count number of tokens
    personMakerspacesCount = len(personMakerspacesList)


    personSubjects = person['Subjects']
    personSubjectsList = personSubjects.split(',')
    personSubjectsCount = len(personSubjectsList)


    personInterests = person['Interests']
    personInterestsList = personInterests.split(',')
    personInterestsCount = len(personInterestsList)

    personPrepared = person['Prepared?']
    personExcited = person['Excited?']

  
    personResponseTotal = personMakerspacesCount + personSubjectsCount + personInterestsCount


    # Now we bring in the older buddies
    for indexOlder, potentialBuddy in olderBuddyData.iterrows():
        rowNumbers = 0
        # Storing the name
        potentialBuddyName = potentialBuddy['Name']
        #potentialBuddyAdvisory = potentialBuddy['Advisory']
    
        # We just need to store the response, not create a list for the older buddies
        potentialBuddyMakerspaces = potentialBuddy['Makerspace']
        potentialBuddySubjects = potentialBuddy['Subjects']
        potentialBuddyInterests = potentialBuddy['Interests']

        potentialBuddyConfident = potentialBuddy['Confident?']
        potentialBuddyExcited = potentialBuddy['Excited?']


        # Initializing the results
        matchTokens = []
        matchScore = 0
        matchScoreTotal = 0


        # Do this for each question
        for i in personMakerspacesList:
            if(potentialBuddyMakerspaces.find(i) != -1):
                matchScore += 1
                matchTokens.append(i)


        for i in personSubjectsList:
            if(potentialBuddySubjects.find(i) != -1):
                matchScore += 1
                matchTokens.append(i)
               
               
        for i in personInterestsList:
            if(potentialBuddyInterests.find(i) != -1):
                matchScore += 1
                matchTokens.append(i)


        # Calculate the match score percentage
        matchScoreTotal = matchScore / personResponseTotal
        matchScoreTotalPercent = round((matchScoreTotal * 100), 5)
        if(matchScoreTotalPercent < 10):
            matchScoreTotalPercent = str(matchScoreTotalPercent)
            matchScoreTotalPercent = "0" + matchScoreTotalPercent
        # Output the results
        #print(f"Match words for {personName} and {potentialBuddyName}: {str(matchTokens)}, {matchScoreTotal}")
        # Store result to streamline writing csv
        matchResult = [personName, potentialBuddyName, matchScoreTotalPercent, matchTokens, personPrepared, potentialBuddyConfident, personExcited, potentialBuddyExcited]


        # Adds new rows to csv
        with open('Advisor1-Advisor2-results-unsorted.csv', 'a') as file:
            writerRow = writer(file)
            writerRow.writerow(matchResult)
            file.close()

# results = csv.reader(open('Test1_2024.csv'), delimiter=',')
# resultsSorted = sorted(results, key=operator.itemgetter(0, 2))
# print(resultsSorted)

results = csv.reader(open('Advisor1-Advisor2-results-unsorted.csv'), delimiter=',')

resultsSorted = sorted(results, key=operator.itemgetter(2), reverse=True)
resultsSorted = sorted(resultsSorted, key=operator.itemgetter(0))

with open('Advisor1-Advisor2-results-sorted.csv', 'w', newline='') as file:
    writerHeader = writer(file)
    # Writing the headers
    writerHeader.writerow(['Younger buddy', 'Older buddy', 'Match score', 'Match phrases', 'Younger buddy prepared?', 'Older buddy confident?', 'Younger buddy excited?', 'Older buddy excited?'])


with open('Advisor1-Advisor2-results-sorted.csv', 'a') as file:
    writeThings = writer(file)
    writeThings.writerows(resultsSorted)
    file.close()
```