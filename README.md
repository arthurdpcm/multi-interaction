# Multi-modal Sequences


++++
<div align="center">
  <h1>Multi-sequence</h1>
  <h4>Software to analyze two individuals interaction.</h4>
  <p><img src="" alt="Multi-sequence logo" width="500px" /></p>

</div>
++++

## Files:

- ## DATA/Merged Interactions:

  ### **What is this Data**?
  This available data was collected by Lise and Mathilde an you can use as examples for your studies.

  ## How your data  **<u>needs</u>** to look like:
  - Your data exported from BORIS or ILAM and also your ethogram file needs to be in the .xlsx format.

  - Your files needs to have this pattern: 01_Mali.Angèle_23.01.23_matin_BI.xlsx. INDEX_FOCAL.INDIVIDUAL_DAY.MONTH.YEAR_MOMENTOFTHEDAY_(BI OR UNI).xlsx. What really matters and NEED to be in that way is the INDEX, FOCAL and INDIVIDUAL.

  - Data file needs to have at least this four columns Subject, Time, Behavior and Behavior type. Any other columns will be disregarded

  - Ethogram file needs to have at least Behavior code and Key columns.

   ## **Check the examples below:**

    ### Dataframe

    | Subject | Time   | Behavior                        | Behavior type |
    |---------|--------|---------------------------------|---------------|
    | Angèle  | 281.640 | t - Tourne la tête vers        | POINT         |
    | Feya    | 281.640 | Entre dans le champs de vision | POINT         |
    | Angèle  | 282.480 | Entre dans le champs de vision | POINT         |
    | Angèle  | 282.720 | g - Grunt supposé              | POINT         |
    | Feya    | 283.080 | K - Se lève à quatre pattes    | POINT         |
    | Feya    | 283.080 | a - Approche                   | START         |
    | Angèle  | 283.920 | l - Lipsmack                   | START         |
    | Angèle  | 284.760 | l - Lipsmack                   | STOP          |


    ### Ethogram
    
    | Behavior code        | Behavior type | Description | Key | Behavioral category | Excluded behaviors |
    | -------------------- | ------------- | ----------- | --- | ------------------- | ------------------ |
    | A - S'assoit         | Point event   |             |  c  |                     |                    |
    | C - Crouch           | State event   |             |  c  |                     |                    |
    | D - Shaking displays | Point event   |             |  c  |                     |                    |
    | G - Se groom         | State event   |             |  c  |                     |                    |



- ## boris2sentence.ipynb: 

    ### Required packages:
    To install packages in python you can use `pip install <package_name>`.

    <ul>
      <li>Pandas</li>
      <li>Os</li>
      <li>Difflib</li>
      <li>Unidecode</li>
      <li>Io</li>
      <li>Datetime</li>
      <li>Copy</li>
      <li>Seaborn</li>
      <li>Math</li>
    </ul>

    This Python script, boris2sentence.py, is designed to convert sequence data extracted from Boris into a more readable format. The script takes a Boris sequence file, an ethogram file, and produces a CSV file containing the obtained sentences.

    ```python
      # Input files
      seqfile = "path/to/boris_sequence.xlsx"
      ethofile = "path/to/ethogram.xlsx"

      # Output file
      output_file = "output_sequence.csv"

      # Convert Boris sequence to sentences
      boris2sentence(seqfile, ethofile, output_file)

    ```

    <br>**1.** Reads the Boris sequence file into a DataFrame.
    <br>**2.** Pre-processes the sequence data, adding "$" in front of each meta-unit and handling specific replacements.
    <br>**3.** Extracts focal, individual, output file name, and output file index from the input sequence file path.
    <br>**4.** Reads the ethogram file into a DataFrame and selects the necessary columns.
    <br>**5.** Builds a dictionary of {units: code name} from the ethogram data.
    <br>**6.** Builds a sequence list by matching behavior codes to the ethogram dictionary.
    <br>**7.** Converts the sequence list into a DataFrame and writes it to the specified CSV output file.


  ## Building **main** file for analysis
  ```python
      directory = r".\DATA\Merged Interactions" 
      ethofile = r".\DATA\Ethogram_Baboons_2024.xlsx"
      output_file = "Outputs/metaunits_all_files_output.csv"

      if os.path.exists(output_file):
          os.remove(output_file)
          
      metaunits=True
      for filename in os.listdir(directory):
          if not filename.startswith(".") :
              sentdf = boris2sentence(os.path.join(directory, filename), ethofile, output_file, metaunits)
  
  ```
  This script is designed to process ethogram data for baboons from multiple interaction files and create a consolidated output in the form of a CSV file named `metaunits_all_files_output.csv` or `np_metaunits_all_files_output.csv`, if you choose or not to count the metaunits which ends with $ (e.g. entre dans le champs de vision) which is an action that is not an interaction. You can make your choice changing the variable metaunits. (True or False)
  
  The input data is sourced from the `.\DATA\Merged Interactions` directory, and the ethogram information is extracted from the `.\DATA\Ethogram_Baboons_2024.xlsx` file.

  ### Usage

  1. Make sure you have the necessary data files in the specified directories:
      - Interaction files in the `.\DATA\Merged Interactions` directory.
      - Ethogram file in the `.\DATA\Ethogram_Baboons_2024.xlsx` file.

  2. Update the following variables in the script if necessary:
      - `directory`: Path to the directory containing merged interaction files.
      - `ethofile`: Path to the ethogram Excel file.
      - `output_file`: Name of the output CSV file.

  3. Run the script. It will process each interaction file, generate ethogram data, and create the output CSV file, that will be useful for your analysis.


  ### Creating the necessary amount of columns

  ```python
    csv_file_path = "Outputs/no_metaunits_all_files_output.csv"

      max_columns = 0
      with open(csv_file_path, 'r') as file:
          for line in file:
              num_columns = len(line.split(',"'))
              max_columns = max(max_columns, num_columns)
      column_names = ['id', 'File name'] + [i for i in range(max_columns-1)]

      sequence_df = pd.read_csv(csv_file_path, delimiter=",", names=column_names)
  ```   

  ### Attention

  This script assumes a specific CSV file structure and naming convention. Adjust the script accordingly if your CSV file has a different format or if the file path is different.


  ## Sequence length

    Here it counts how many actions has in the sequence using the function ```.iloc[:,2:]``` to select the row but from the third to the last column. Then we use ```.count(axis=1)```.

    ![Axis explanation](https://i.stack.imgur.com/Z29Nn.jpg)
    
  ```python
    output_file = "Outputs/sequence+len.csv"
    len_sequence_df = sequence_df.copy()
    len_sequence_df["sequence_len"] = len_sequence_df.iloc[:, 2:].count(axis=1)
    len_sequence_df = len_sequence_df.drop(0)
    if os.path.exists(output_file):
        os.remove(output_file)
    len_sequence_df.to_csv(output_file)
    len_sequence_df.head(5)
  ```

  ### Sequence, Length and Tokens

    This part of the code we are focused on get which actions are from each individual (focal or individual), and the tokens (tokens are unique units emitted by an individual, so if it emitts 2 times fA, it's just one unit, but 2 actions.)

  ## Overlap

  The function ```boris2sentence_overlap(seqfile, ethofile, output_file): ``` is responsable to generate a csv file with the sequences with overlap. It's output looks like this: 
  ```
    "[('i', 'ft')]","[('i', 'ft'), ('f', 'ft')]","[('i', 'ft'), ('f', 'ft'), ('f', 'ca')]","[('i', 'ft'), ('f', 'ft'), ('f', 'ca'), ('f', 'fo')]","[('i', 'ft'), ('f', 'ft'), ('f', 'ca')]","[('i', 'ft'), ('f', 'ft'), ('f', 'ca'), ('i', 'fl')]","[('i', 'ft'), ('f', 'ft'), ('f', 'ca'), ('i', 'fl'), ('f', 'fl')]","[('i', 'ft'), ('f', 'ft'), ('f', 'ca'), ('f', 'fl')]","[('i', 'ft'), ('f', 'ft'), ('f', 'ca'), ('f', 'fl'), ('f', 'cs')]","[('f', 'ft'), ('f', 'ca'), ('f', 'fl'), ('f', 'cs')]","[('f', 'ft'), ('f', 'fl'), ('f', 'cs')]","[('f', 'fl'), ('f', 'cs')]","[('f', 'cs')]",[],"[('f', 'cU')]","[('f', 'cU'), ('i', 'cÃ©')]","[('f', 'cU')]",[],"[('i', 'ct')]",[],"[('i', 'cÃ©')]",[],"[('i', 'cl')]"
  ```

    This output is done using the column Behavior Type and analyzes when it's START, STOP or POINT. When an action start, it is added to the array, and it is removed just when it stop. When is a POINT action, we add the array with the POINT action and then we pop it out and add the array without it again.

    ### Usage

    ```python
        directory = "DATA/Merged Interactions/"
        ethofile = "DATA/Ethogram_Baboons_2024.xlsx"
        output_file = "Outputs/overlap_test.csv"

        if os.path.exists(output_file):
            os.remove(output_file)
        for filename in os.listdir(directory): 
            # checking if it is a file
            if not filename.startswith(".") :
                sentdf = boris2sentence_overlap(os.path.join(directory, filename), ethofile, output_file)

    ```

    To read the .csv generated we can follow these lines. 
    
    Possible error: Error tokenizing data. C error: Expected 63 fields in line 214, saw 77
    If this error happens you will need to change the following line: ```max_columns - 1```. and, instead of -1, you'll need to add as many as are needed for the expected fields to reach as many as have been seen. 

    ```python
        sequences_overlap_file = "Outputs/overlap_test.csv"
        max_columns = 0
        with open(sequences_overlap_file, 'r') as file:
            for line in file:
                num_columns = len(line.split(',"'))
                max_columns = max(max_columns, num_columns)
        column_names = ['id', 'File name'] + [i for i in range(max_columns - 1)]
        overlap_csv = pd.read_csv(sequences_overlap_file, names=column_names)
    
    ```
