[< Multilingual Labels](develop_multilingual_labels.md) 
# Multilingual - Storing Labels


In our framework, we track the languages that have been registered for use, and interface labels to display for our applications.

### Languages
Our languages are defined in the `site_multilingual_language` table, and has the following definition:
```json
{
	// i18n language code:  'en', 'ko', 'zh-hans', etc... 
	language_code : {
	    type : "string",
	    size : 10
	}, 

	// a label for the language: 'english', 'korean', '中文'
	language_label : {
	    type : "text"
	} 
}
```



### Labels

- label description

- 


[< Multilingual Labels](develop_multilingual_labels.md)     
Next: [ToDo: content](develop_multilingual_02_content.md)