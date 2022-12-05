# IPYNB Notebooks (pipeline to convert .pdf to .csv)

In order for these scripts to work,<b>the directory must contain the following folders</b>:
- <b>bibles</b>: where all the pdf are stored (used by "scraping bibles.ipynb", which has as output folder the following folder);
- <b>csv bibles</b>: where temporary files are stored (used by "pdf to csv (only one column).ipynb" or "New scraping.ipynb"). File must be moved by hand to the following folder.
- <b>csv_folders</b>: where csvs are stored in order to be cleaned (used as input folder by "cleaning csvs.ipynb" or "spacy.ipynb");
- <b>clean_csv_folders</b>: where final csvs are stored (used as output folder by "cleaning csvs.ipynb")
- <b>NEW_csv_folders</b>: where "New scraping.ipynb" and "spacy.ipynb" are stored and handled.
