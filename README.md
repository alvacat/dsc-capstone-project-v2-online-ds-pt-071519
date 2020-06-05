# Magic: The Card Price Predictor

## Table of Contents
- Blog: [The Magic of Collaboration](https://alvacat.github.io/the_magic_of_collaboration)
- YouTube Code walkthrough: [https://youtu.be/E4MOJo-usHc](https://youtu.be/E4MOJo-usHc)
- This very repository!
 - readme.md: This very file!  Gives an overview of the project and its components.
 - images: This folder contains the images used in this project. (Spoiler: It’s 99% Magic cards.)
 - capstone_project.ipynb: The notebook file that has all of the code for the project.
 - appendix.md: A list of each artist, layout, etc split into quartile groups by their median card price. These were used during the project but obscured by the code.  This appendix is intended as a reference for people who are curious where each artist, layout, etc falls in terms of median card price.
 - MagicThePresentation (.ppt and .pdf): A presentation aimed primarily at Magic investors that describes the project and its outcomes as well as making recommendations for future investments.
 - Allprintings.sqlite: The actual SQL database from whence the data came.
 - scryfallUrls.csv: A list of the URLS for split and aftermath cards
 - split_colors.pkl: A pickled dataframe of colors for the split/aftermath cards (to save me from having to query a website repeatedly).

## About the project

This project focuses on cards in the collectible card game, Magic the Gathering.  The big question is “which cards are the best?” which will be answered from the investor’s perspective (with a look at the player’s perspective in a potential future project).  For the investor, “best” means “which cards are worth the most?” It’s easy enough to look up prices of cards and figure out which one is currently worth the most (mint condition beta Black Lotus: $100,000).  The aim of this project is not to do simple research about current card prices, however, but to predict which cards will be worth the most from newly released sets.

The raw data was cleaned and processed with particular attention being paid to the colors in the cost of the card and the words in the card’s text.  There’s an abundance of data available for each card and, to avoid the curse of dimensionality, several pieces of data were binned into four groups based on their median prices.  After that, an XGBoost model was created, validated, and improved with a gridsearch before being applied to a test set.  Based on the model, card prices are most affected by the age of the card and the converted Mana cost. Cards that are reserved (i.e. they won’t be printed again) are also worth more.  Therefore, if you’re looking to invest in Magic cards, keep an eye out for old cards with a high converted Mana cost.  If you’re looking to buy new Magic cards, keep the Mana cost in mind as well as the particular words that are in the card’s text and the formats in which a card is legal.  All of those things contribute to a higher value.

## Methods in project

- colorCost(card): Takes the mana cost for a given card and converts it from a string to a dictionary of the colors in the Mana cost. card = the index for a card. Returns a dictionary of colors in the card's cost.
- colorCostFromUrl(card_index,meta): Uses the metadata from a website to determine the color cost of a card.  meta = string that’s retrieved from the website that contains the color cost. Returns a dictionary of colors.
- getManaCost(card_index): Scrapes scryfall.com to get the mana cost for the given card. card = the index of the card in the weird_cards_url dataframe. Returns a dictionary of colors.
- quantileSort(feature): Finds the median price for each category in the given feature and then splits the feature into 4 different groups based on the quartiles for the median prices.
- encoder(feature,groups): Takes in a given feature and the four quartile groups.  One-hot encodes the feature, adds those column to the original dataframe, drops the original feature column.  Feature is the column name. Groups is a list of the lists of quartile categories.
- median_price(word): Finds the median price of cards with text that contains the given word. Returns the median price. Note: this is a very slow way to get all the median prices.  The next method is much quicker.
- find_word_prices(card): Finds all the prices from cards that feature the given word. Returns a list of prices.
- word_quantiles(card): Replaces words in the card's text with its corresponding quantile. (A quantile is based on the median price of cards with that word in its text.)  Returns the quantile-ified text.
- preprocess_ohe_feature(feature,feature_group,df): Takes in a feature, the items for the feature split into 4 quartile groups based on the training dataset, and a dataframe.  One-hot-encodes the given dataframe for the given feature into the given quartile groups. Returns the updated dataframe.
- preprocess_ohe(features,feature_groups,df): Takes in a list of features, their corresponding quartile groups and a dataframe.  One-hot-encodes the given dataframe for each of the given features into the given quartile groups.  Returns the update dataframe.
- preprocess_tokenize(df): Takes in a dataframe and transforms the text from each card into counts of word quartiles.