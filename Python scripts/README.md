# IPYNB Notebooks (pipeline to convert .pdf to .csv)

In order for these scripts to work,<b>the directory must contain the following folders</b>:
- <b>bibles</b>: where all the pdf are stored (used by "scraping bibles.ipynb");
- <b>csv bibles</b>: where temporary files are stored (used by "pdf to csv (only one column).ipynb"). File must be moved by hand to the following folder.
- <b>csv_folders</b>: where csvs are stored in order to be cleaned (used as input by "cleaning csvs.ipynb");
- <b>clean_csv_folders</b>: where final csvs are stored (used as output by "cleaning csvs.ipynb")
