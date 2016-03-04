# Setting up i18n scripts

Scripts used to perform i18n will be generated during a bungeni buildout. These scripts are available in the `bin` directory after a bungeni buildout.

These scripts require gettext. Run this command in the terminal to install it.
```
   sudo apt-get install gettext
```


NOTE: The below scripts cannot be run when the Bungeni service is running, You will need to stop the Bungeni service to be able to run these scripts successfully.

# Generate i18n catalogs

To generate the POT file run the following commands

```
./bin/restore-localization-forms-defaults
./bin/i18nextract
```

The POT file will be created in locales directory in `src/bungeni_custom/translations/bungeni/`

# Merge old catalogs with new ones

Then run this command to merge the pot file with the po files in the bungeni translations directory
```
   ./bin/i18nmergeall
```

Alternatively, use the msgmerge tool directly to merge the old translations with the new ones for each PO files e.g. for the en translation catalog for bungeni.ui

```
cd src/bungeni.main/bungeni/locales/
msgmerge -U en/LC_MESSAGES/bungeni.po bungeni.pot
```

# Initialize a new message catalog
The i18nmergeall script above will only merge with the catalogs in the locales directory (`src/bungeni_custom/translations/bungeni/`).
To initialize a new catalog, add the locale code as described here [HowTo\_CustomizeParliamentaryInformationManagement#How\_to\_configure\_languages](HowTo_CustomizeParliamentaryInformationManagement#How_to_configure_languages.md) and then run

```
  ./bin/i18n-init {{LOCALE_CODE}}
```

## LOCALE\_CODE examples

| **Language** | en | fr |
|:-------------|:---|:---|
| **Language + Region** | en-ke | fr-rw |


This will initialize the provided locale's message catalog (PO files) synced to the bungeni POT file. This catalog can then be managed using tools such as [Poedit](http://www.poedit.net) or [Pootle](http://translate.sourceforge.net/wiki/pootle/index)