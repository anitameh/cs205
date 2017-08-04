## Data

The data contains information about the [Million Song Dataset](http://labrosa.ee.columbia.edu/millionsong/pages/getting-dataset), a publicly avaiable ~600 GB dataset (uncompressed), collected as a result of a collaboration between [The Echo Nest](http://echonest.com/) and LabROSA at Columbia University, with some information also coming from [MusicBrainz](http://musicbrainz.org/). The information below is specific to the project here; for general information about the raw data, please see https://github.com/tb2332/MSongsDB.

### Creation

The Million Song Data Set (MSD) contains 1,000,000 songs, chosen using the following process:

1. Determine the  most ‘familiar’ artists, as defined by The Echo Nest. Select as many songs as possible from each.
2. Determine the 200 top terms from The Echo Nest. Using each term as a descriptor to find 100 artists, select as many of their songs as possible.
3. Select songs and artists from the CAL500 dataset (see [here](http://slam.iis.sinica.edu.tw/demo/CAL500exp/)).
4. Select ‘extreme’ songs from The Echo Nest search params, e.g. songs with `highest energy`, `lowest energy`, `tempo`, `song hotttnesss`, etc.
5. Construct a random walk along the similar artists links starting from the 100 most familiar artists. Select as many songs as are necessary to reach 1,000,000.

After Step 1, there are approximately 8,950 songs. After Step 3, there are approximately 23,950 songs. An additional ~500,000 songs were then added before starting Step 5. The Python code used to create the data can be found [here](https://github.com/tb2332/MSongsDB/tree/master/PythonSrc/DatasetCreation).

### Location and File Structure

The data is stored in `/scratch/global/millionsong/data`.

The file paths were purposely branched multiple times by the creators in order to minimize access slow-down. The directory structure is based on the Echo Nest track ID’s. Each ID is a hashcode, and always take the form `TR + <LETTERS> + <LETTERS & NUMBERS>`.

* The directory path is the third, fourth and fifth letters from the track ID The file itself is named after its track ID (plus the extension `.h5`).
* `/Additional Files` has metadata information, a `README`, etc. 
* `/data` contains a subdirectory for each letter of the alphabet, A-Z. An example path is `A/D/H/TRADHRX12903CD3866.h5`. The full path would therefore be `/millionsong/data/A/D/H/TRADHRX12903CD3866.h5`.

### Model

A list of the features (aka `fields`) available in the files of the dataset is listed in the table below. Detailed information about the data types, descriptions and corresponding Echo Nest API reference links can be found [here](http://labrosa.ee.columbia.edu/millionsong/pages/field-list).

An example song’s features can be found [here](http://labrosa.ee.columbia.edu/millionsong/pages/example-track-description).

| Fields 1-15 | Fields 16-30 | Fields 31-45 | Fields 45-54 |
|-------------|--------------|--------------|--------------|
| analysis sample rate | artist terms weight | release | song id |
| artist 7digitalid | audio md5 | release 7digitalid | start of fadeout |
| artist familiarity | bars confidence | sections confidence | tatums confidence |
| artist hotness | bars start | sections start | tatums start |
| artist id | beats confidence | segments confidence | tempo |
| artist latitude | beat start | segments loudness max | time signature |
| artist location | danceability | segments loudness max time | time signature confidence | 
| artist longitude | duration | segments loudness max start | title |
| artist mbid | end of fadein | segments pitches | track id |
| artist mbtags | energy | segments start | track 7digitalid |
| artist mbtags count | key | segments timbre | year |
| artist name | key confidence | similar artists | |
| artist playmeid | loudness | song hotttness | |
| artist terms | mode | song id | |
| artist terms freq | mode confidence | start of fadeout | |


