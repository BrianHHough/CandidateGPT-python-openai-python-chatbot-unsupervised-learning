# CandidateGPT 🇺🇸

**CandidateGPT** is a python-based chatbot built around eight U.S. presidential candidates to allow a user to ask questions about a candidate's political positions. Each candidate has a corresponding embeddings vector database based on publicly available data found on the candidate's corresponding wikipedia page. A subset of candidates was selected from the Wikipedia page, ["2024_United_States_presidential_election"](https://en.wikipedia.org/wiki/2024_United_States_presidential_election), upon which to build the various vector databases.

## Disclaimer:
⚠️ IMPORTANT: This project, the code in it, and outputs, are NOT IN ANY WAY an endorsement of any of the following candidates, their policies, or beliefs. This project was solely an experiment into using multiple vector databases with embeddings to practice semantic text search querying against OpenAI's `text-davinci-003` completion model for generative AI text generation.

## Use-Cases:
- Utilizing generative AI to increase civic engagement
- Augment a given voter's understandings about the policy positions of their candidates
- Provide concise, right-to-the-point, explanations about complex issues
- Improve AC (Augmented Communication) between voters and the candidates

## Tools used:
- AI API: `OpenAI`
- Data Manipulation Library: `Pandas`
- Development/Documentation: `Jupyter Notebook`
- Model for Completions: `text-davinci-003`
- Model for Embeddings: `text-embedding-ada-002`
- Programming Language: `Python`
- Tokenizer: `tokenizer`
- Data Sources: `Wikipedia`

## Data sources:
- [Joe Biden](https://en.wikipedia.org/wiki/Joe_Biden)
- [Ron DeSantis](https://en.wikipedia.org/wiki/Ron_DeSantis)
- [Nikki Haley](https://en.wikipedia.org/wiki/Nikki_Haley)
- [Robert F. Kennedy Jr.](https://en.wikipedia.org/wiki/Robert_F._Kennedy_Jr.)
- [Mike Pence](https://en.wikipedia.org/wiki/Mike_Pence)
- [Vivek Ramaswamy](https://en.wikipedia.org/wiki/Vivek_Ramaswamy)
- [Donald Trump](https://en.wikipedia.org/wiki/Donald_Trump)
- [Marianne Williamson](https://en.wikipedia.org/wiki/Marianne_Williamson)

## Code Details
- To gather an initial dataset, each candidate's Wikipedia page is loaded into an `embeddings.csv` file with 3 columns:
    - index
    - text
    - embeddings
- To order the dataset based on those embeddings, each `embeddings.csv` file is transformed into a `distances.csv` file with 4 columns:
    - index
    - text
    - embeddings
    - distances
- The distances are what will be used to allow our model to find data that is most relevant as "context" when submitting the Zero Shot Prompt into the generative text model.
- For data wrangling, each candidate's dataset is loaded into a `pandas` dataframe with 4 columns: 
    - index
    - text
    - embeddings
    - distances
- The user then must pass in 2 values to a custom query along with a for-looped context array to OpenAI for custom query completion:
    1. `SELECTED_PARAMS_CANDIDATE`
        - This is a camelcase variable listed in the `candidate_params` Python dictionary
        - This variable is connected to the `SELECTED_PARAMS_CANDIATE_FULLNAME` variable which looks up its corresonding value in the `candiate_lookup` directory for the "fullName" of that candidate
    2. `USER_QUESTION_INPUT` 
        - This is a camelcase variable listed in the `candidate_questions_testing` Python dictionary
        - I used this dictionary for testing, but users could type in their own question as well.

## Scenario Details + Dataset Considerations
- In the Jupyter Notebook code below, it shows 2 presidential candidates Joe Biden and Nikki Haley, being asked about their stances on `discrimination` and `foreign policy`.
    - ❌ Without vector database training the model:
        - Example #1 Pt.1: Nikki Haley (UNTRAINED) - discrimination
            - Couldn't generate a response (lack of context?)
        - Example #1 Pt.2: Nikki Haley (UNTRAINED) - foreign policy
            - Couldn't generate a response (lack of context?)

        - Example #2 Pt.1: Joe Biden (UNTRAINED) - discrimination
            - Answer is very general/broad
        - Example #2 Pt.2: Joe Biden (UNTRAINED) - foreign policy
            - Answer is very general/broad
    - ✅ With vector database training the model:
        - Example #3: Nikki Haley (TRAINED) - discrimination
            - Answer is crisp, clear, and detail-oriented
        - Example #4: Joe Biden (TRAINED) - discrimination
            - Answer is crisp, clear, and detail-oriented

        - Example #5: Nikki Haley (TRAINED) - foreign policy
            - Answer is crisp, clear, and detail-oriented
        - Example #6: Joe Biden (TRAINED) - foreign policy
            - Answer is crisp, clear, and detail-oriented
    
- At the end of the Jupyter Notebook, I created a 
- For this application, I created separate vector databases for each of the candidates for the following reasons:
    - Since OpenAI's models are only trained up until 2021, there needs to be a way to fill in the gaps of the model should there not be data available to return a response
    - Since there isn't already that additional context (because the candidates are running for president in 2024 and the model wouldn't have that yet), we can easily pull data from Wikipedia's API to generate a .csv file for our vector database of each candidate.
    - I then map over the distance-sorted embeddings to put in the context up until the token limit that the model will allow. This is important so that I can send the max amount of data possible to the model for background context so that it can sort it, add it to its schema, and then return a well-worded and articulated response back to the user who asked the question.
    - An assumption I made is that instead of jamming all of the candidates' data into one database, creating separate databases for each candidate would increase reliability of answers and speed of delivery, while cutting down on hallucinations wherever possible. 
        - I assume that as the data grows, it would be too burdonsome to re-create a mega-embeddings file, rather than creating embeddings and related vector embeddings distance files for each candidate in consideration. 
        - Also, since the prompts are designed to be one candidate + one topic per query, there would not be a need to cross-over and compare different candidates' datasets.

## Cost Considerations
- When working with tokens, especially in a testing setting, its easy to forget how many times a prompt was run or sent to the OpenAI API. So far, to run this lab, including the setup, tests, and shipping of this project, the total OpenAI costs have reached $1.91. To navigate around costs, these are some strategies I implemented while I built this project:
    - Tried to print out values, dictionaries, and outputs without using any openai methods as much as possible
    - Tried to test very small inputs to the model when I was testing
    - Kept the OpenAI usage graph open and checked the billing after almost every call to see how various calls impacted price
    - Included a price limit to my organization account to ensure there couldn't be a very large bill all of a sudden