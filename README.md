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

**Note**: The rank information is derived from the solo/duo rank of the first player found in each match. While this may not be the most accurate method, it is more efficient than querying the rank information for all 10 players, which would require 10 times as many API calls.

**Example**:
```python
main("Wmig-c4JTkPXHkAJJ8NCEUE5FFpJyFfF0IpwXQfI3geaYExpuGTHUGON2EW4TnGOSqLY5LHxCL0xUQ")
```

### Creating Model

This section describes the usage of the code for processing the data such as encoding data, transforming data, training the models, testing, and predicting. The algorithm will train models to predict outcome of drafts depending on some parameters. So the model to predict games from NA will be different from a model predicting games from EU as metas differ in different regions, elos, and game versions. 

1. **Navigate to ModelSetup.ipynb**
2. **Fill in parameters**: `def main(blue_team, red_team,region,game_mode,elo,version, threshold,batch_size, num_epochs, override)`:
    - Italicized parameters are optional.
    - blue_team: (Possible entries = ['Naafiri', 'Braum', 'Kayn', 'Kled', 'Smolder'] | format = array)
    - red_team: (Possible entries = ['Akshan', 'Viego', 'Leona', 'Camille', 'Aphelios'] | format = array)
    - region: (Possible entries = 'ANY', 'NA1', 'EUW1', 'KR', 'BR1', etc. | format = string | default = 'NA1')
    - game_mode: (Possible entries = 'ANY', 'ARAM', 'CLASSIC' | format = string | default = 'ARAM')
    - elo: (Possible entries = 'ANY', 'BRONZE', 'GOLD', etc. | format = string | default = 'ANY')
    - version: (Possible entries = 14.13, 14.12 | format = string | default = 14.13)
    - threshold: (Possible values = any number from 1 to 10 | format = int | default = 5)
    - batch_size: (Possible values = any int | format = int | default = 1)
    - num_epochs: (Possible recommended values = any int | format = int | default = 10)
    - override: (Possible values = True, False | format = bool | default = False)

**example usage**: 
```python
blue_team = ['Naafiri','Braum','Kayn','Kled','Smolder']
red_team = ['Akshan','Viego','Leona','Camille','Aphelios']
main(blue_team, red_team,'NA1','ANY','ANY','14.13', threshold=5, batch_size=1, num_epochs=10, override=False)
```

## Features

## Pipeline

### Model

## Contributing
