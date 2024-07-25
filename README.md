# Coming up with a name rn

## Installation

```bash
git clone https://github.com/johnsony0/Draft-Analysis.git
cd Draft-Analysis.git
pip install -r requirements.txt
```

## Usage

### Prerequisite

Before running the project, ensure you have the following:

1. **Riot API Key**: Sign up at [Riot Developer Portal](https://developer.riotgames.com/) to get your API key.

2. **PostgresSQL**: Ensure you have [PostgreSQL](https://www.postgresql.org/download/) installed on your system.

3. **Environment Variables**: Use the environment template below to create your .env file in the root of your project.

```env
RIOT_API_KEY = 'YOUR_RIOT_API_KEY'

DB_NAME = 'YOUR_DATABASE_NAME'
DB_HOST = 'localhost'
DB_USER = 'postgres'
DB_PASSWORD = 'YOUR_DATABASE_PASSWORD'
DB_PORT = '5432'
```

### Gathering Data

This section describes the process of collecting match data using the Riot API and storing it in a PostgreSQL database. The provided code will automatically set up the necessary table and populate it with data.

1. **Navigate to DatabaseSetup.ipynb**

2. **Obtain a PUUID**: Get the PUUID of a 'root player' (this can be yourself, a pro player, or any other player) by using the [Riot API website](https://developer.riotgames.com/apis#account-v1/GET_getByRiotId). For example, if your username is 'Player #NA1' your input should match the example below, the PUUID will be found in the response body.
![image](https://github.com/user-attachments/assets/f5a43645-563e-4506-b89d-8dd309ffa4a5)
![image](https://github.com/user-attachments/assets/57621878-91d5-4534-8841-50e79398a8b1)

3. **Run the Main Function**: Call the `main` function with the obtained PUUID. The algorithm will then collect match data for the root player and other players in their games. It ensures no duplicate players or matches are recorded using sets. The dataset will throw an error if an identical match ID is encountered. The diagram depicts the first iteration of the algorithm.
![image](https://github.com/user-attachments/assets/55808403-fbd4-4877-87de-9e3965bdd91f)

**Notes**: The rank information is derived from the solo/duo rank of the first player found in each match. While this may not be the most accurate method, it is more efficient than querying the rank information for all 10 players, which would require 10 times as many API calls. The algorithm is set to end after 

**Example**:
```python
main("Wmig-c4JTkPXHkAJJ8NCEUE5FFpJyFfF0IpwXQfI3geaYExpuGTHUGON2EW4TnGOSqLY5LHxCL0xUQ")
```

### Creating Model

This section describes the usage of the code for processing the data such as encoding data, transforming data, training the models, testing, and predicting. The algorithm will train models to predict outcome of drafts depending on some parameters. So the model to predict games from NA will be different from a model predicting games from EU as metas differ in different regions, elos, and game versions. 

1. **Navigate to ModelSetup.ipynb**
2. **Fill in parameters** when calling `def main(blue_team, red_team,region,game_mode,elo,version, threshold,batch_size, num_epochs, override)` Italicized parameters are mandatory:
    - *blue_team*: (a possible entry = `['Naafiri', 'Braum', 'Kayn', 'Kled', 'Smolder']` | format = array of strings)
    - *red_team*: (a possible entry = `['Akshan', 'Viego', 'Leona', 'Camille', 'Aphelios']` | format = array of strings)
    - region: (some possible entries = `'ANY', 'NA1', 'EUW1', 'KR', 'BR1', etc.` | format = string | default = 'NA1')
    - game_mode: (all possible entries = `'ANY', 'ARAM', 'CLASSIC'` | format = string | default = 'ARAM')
    - elo: (some possible entries = `'ANY', 'BRONZE', 'GOLD', etc.` | format = string | default = 'ANY')
    - version: (all possible entries = `'ANY', '14.13', '14.12'` | format = string | default = 14.13)
    - threshold: Specifies the minimum number of matching champions on a team required to consider a game relevant. For example, setting a threshold of five means the function will return all games where a team has five or more champions that match the specified champions. (Possible values = any number from 1 to 10 | format = int | default = 5)
    - batch_size: (recommended values = `1,2,5` | format = int | default = 1)
    - num_epochs: (recommended values = `5,10,20,50,100` | format = int | default = 10)
    - override: Determines whether to train a new model to override the old one or not (all possible values = `True, False` | format = bool | default = False)

**Example Usage**: 
```python
blue_team = ['Naafiri','Braum','Kayn','Kled','Smolder']
red_team = ['Akshan','Viego','Leona','Camille','Aphelios']
main(blue_team, red_team,'NA1','ANY','ANY','14.13', threshold=5, batch_size=1, num_epochs=10, override=False)
```

## Features
- Creates and populates SQL table with match stats such as id, champions, region, elo, etc
- Encodes champion names with bag of words
- Initializes neural network, support vector machine, and naive gaussian bayes
- Trains and tests all models
- Batch inputs for quicker training and testing
- GPU support
- All models have between 53-55% accuracy (>50 is good) 
- Uses voting as an ensemble method
- Given two teams, predict which will win
- Modularized, with pipeline in main()
- Thousands of match data in csvs
- Pre-trained models

## Pipeline

Depicted below is the general pipeline of this ml project, showing which functions perform which module of the pipeline.
![image](https://github.com/user-attachments/assets/14f55f7b-daf1-45a6-b0eb-d731e061f7c0)

### Encoding

This method transforms an input list of champions into a binary array. Each position in the array corresponds to a champion from a predefined list. If an input champion matches a champion in the predefined list, its position in the array is set to 1; otherwise, it remains 0. The sklearn input is an array so we append the blue_team and red_team encoded data, while the PyTorch input is a 2d array so we can vertical stack the blue_team and red_team encoded data, as visualized below.

![image](https://github.com/user-attachments/assets/e9f4e5e4-e69b-448b-8fe0-e4deb3b0d391)

### Model

The encoded data is put into three models: linear support vector classification, gaussian naive bayes classification and a convolutional neural network. The output of each of these are put into a voting ensemble with the most frequent output being the final output selected.

The convolutional model consists of convolution layers followed by ReLUs, and a flatten before fully connected layers as shown. 

![image](https://github.com/user-attachments/assets/15da7cf1-127b-47d9-9d5b-c1050963d130)

## Future Work
- Improve NN performance
- Different ensemble learning method
- Different encoding
- Add more data such as runes, player ranks, to help predict outcome

## Contributing
Pull requests are welcome.

For suggestions feel free to send me an email from my website.

Likewise for bugs/errors, or report it as an issue.

Feel free to use this code, but please give credits.

Thanks :D
