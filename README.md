CKEditor 5 headings feature with ID
========================================

[![npm version](https://badge.fury.io/js/%40ckeditor%2Fckeditor5-heading.svg)](https://www.npmjs.com/package/@ckeditor/ckeditor5-heading)
[![Dependency Status](https://david-dm.org/ckeditor/ckeditor5-heading/status.svg)](https://david-dm.org/ckeditor/ckeditor5-heading)
[![devDependency Status](https://david-dm.org/ckeditor/ckeditor5-heading/dev-status.svg)](https://david-dm.org/ckeditor/ckeditor5-heading?type=dev)

This package implements the headings feature for CKEditor 5 but with the ID attribute allowed. As of version 16.0.0, this package will generate a random token when new headings are inserted.

# Documentation

See the [`@ckeditor/ckeditor5-heading` package](https://ckeditor.com/docs/ckeditor5/latest/api/heading.html) page in [CKEditor 5 documentation](https://ckeditor.com/docs/ckeditor5/latest/).

### For correct functionality

Set the input-field which gives the article title to the CKEditor-Headings
Please set the HTML-Element type to the Input! 
````````
# = id
. = class
````````
`````
#pageTitle
`````
When you don't give that, it's not working!

### How give the current ID to CK-Editor
Give the desired ID or class to the CKEditor using the "editor" variable.
You must use the function!

````````
let pageTitleId = this.editor.titleHtmlId;
````````

Example from Helpmanager:
````````
async setEditor() {
        const { default: GeoinfoEditor } = await import(/* webpackChunkName: "ckeditor" */ "@ckeditorGeoinfo/ckeditor5-build-geoinfo");
        GeoinfoEditor
            .create(document.querySelector(this.editorHtmlId))
            .then((editor) => {
                this.$scope.editor = editor;
                editor.titleHtmlId = this.titleHtmlId;
                console.log("Editor is ready");
            })
            .catch((error) => {
                console.error(error.stack);
            });
    }
````````


## Changes by EicD

Important don't delete this Code when you update the Version to a newer one!

Call function in CK Editor
````````
model.change(writer => {
			const blocks = Array.from(document.selection.getSelectedBlocks())
				.filter(block => {
					return checkCanBecomeHeading(block, modelElement, model.schema);
				});

			for (const block of blocks) {
				if (!block.is("element", modelElement)) {
					writer.rename(block, modelElement);

					// Write the ID if it doesn't already have one
					if (isHeading) {
						writer.setAttribute("id", this.generateToken(), block);
					}
				}
			}
		});
````````

----------------------------------------------------

Pure function for manipulation from String
````````
    getMarketText() {
		const selection = this.editor.model.document.selection;
		const range = selection.getFirstRange();

		for (const item of range.getItems()) {
			return item.data;
		}
	}

	getPageTitleText() {
            let pageTitleId = this.editor.titleHtmlId;
            let titleText = document.querySelector(pageTitleId).innerText;
            if (titleText === "") {
                titleText = document.querySelector(pageTitleId).value;
                if (titleText === "") {
                    alert("\nBitte gib deinem Artikel zuerst einen Titel\n(Oberhalb des \"Editors\")\nDanke!");
                }
            }
            return titleText;
    	}

	removeSpecialCharacters(string) {
		let tmp = string;
                tmp = tmp.replace(/[`~!@#$%^&*()_|+=?;:'",.<>\{\}\[\]\\\/]/gi, "");
                tmp = tmp.replace(/ä/g, "ae");
                tmp = tmp.replace(/Ä/g, "Ae");
                tmp = tmp.replace(/ö/g, "oe");
                tmp = tmp.replace(/Ö/g, "Oe");
                tmp = tmp.replace(/ü/g, "ue");
                tmp = tmp.replace(/Ü/g, "Ue");
                tmp = tmp.replace(/ß/g, "ss");
        		return tmp;
	}

	setFirstLetterCapitalized(string) {
		let tmp = string;
		tmp = tmp[0].toUpperCase() + string.slice(1);
        tmp = tmp.replace("\-", " ");
		tmp = tmp.replace(/\b\w/g, l => l.toUpperCase())
		return tmp;
	}

	removeAllSpaces(string) {
		let tmp = string.trim();
		return tmp.split(/\s/).join("");
	}

	designedString(string) {
		let tmp = string;
		tmp = this.removeSpecialCharacters(tmp);
		tmp = this.setFirstLetterCapitalized(tmp);
		tmp = this.removeAllSpaces(tmp);
		return tmp;
	}

	generateToken() {
    		const pageTitle = this.designedString(this.getPageTitleText());
    		const marketText = this.designedString(this.getMarketText());
    
    		return `${pageTitle}_${marketText}`;
    	}
````````

Changes are in the File ````headingcommand.js````
