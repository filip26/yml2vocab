# Generate RDFS vocabulary files from CSV

The script in the directory generates RDFS vocabulary files in JSON and Turtle formats, plus a human readable HTML file containing the vocabulary in RDFa, based on a simple vocabulary definition in a CSV file. Neither the script nor the CSV format is prepared for complex vocabularies; its primary goal is to simplify the generation of simple, straightforward RDFS vocabularies without, for instance, sophisticated OWL statements.

When running, the script relies on two files:

1. The `vocabulary.csv` file, containing the definition cells for the vocabulary.
2. The `template.html` file, used to create the HTML file version of the vocabulary.

## Definition of the vocabulary in the CSV file

The vocabulary is defined in a CSV file, which contains the following columns: `category`, `id`, `property`, `value`, `label`, `upper value`, `domain`, `range`, `deprecated`, `comment`, and `see also`. The `category` column defines the category of the row, and the interpretation of the row's content (that is, of the other columns) depends on this category. 

The `comment` cells may include HTML tags; these will be filtered out for Turtle and JSON-LD, but will be copied into HTML (note, b.t.w., that the markdown syntax for simple formatting, like the use of "`" for code, may also be used).

The `see also` cells may contain a comma separated list of hyperlinks following the Markdown syntax, i.e., `[label of reference](URL)`.

The available categories, specified by the `category` cells, and the corresponding interpretation of the columns, are:

- `vocab`: the prefix and the URL of the vocabulary that is being specified are provided in the `id` and `value` columns, respectively.
- `prefix`: definition of a prefix, and corresponding URL, for each external external vocabulary in use, defined by the `id` and `value` columns, respectively. 
    
    Note that some prefix/value pairs are defined by default, and it is not necessary to define them here. These are: `dc` (for `http://purl.org/dc/terms/`), `owl` (for `http://www.w3.org/2002/07/owl#`), `rdf` (for `http://www.w3.org/1999/02/22-rdf-syntax-ns#`), `rdfs` (for `http://www.w3.org/2000/01/rdf-schema#`), and `xsd` (for `http://www.w3.org/2001/XMLSchema#`).
- `ontology`: definition of an "ontology property", that is, a statement made about the vocabulary itself. The (prefixed) property term is defined in the `property` column, and the value in the `value` column. If the value can be parsed as a URL, it is considered to be the URL of an external resource; otherwise, the value is considered to be (English) text.

    It is good practice to provide, at least, `dc:description` as an ontology property with a short description of the vocabulary.

- `class`: definition of a class. The `id` column defines the class name (no prefix should be used here); possible superclasses are defined in the `upper value` column as a single term, or as a comma-separated list of terms. There should be a label and a longer description in English text provided in the `label` and `comment` columns, respectively. The `see also` column may provide a set of extra links; these are translated into `rdfs:seeAlso` statements in the vocabulary.
 
    A class can be declared as deprecated by setting its `deprecated` column to "yes".
- `property`: definition of a property. The `id` column defines the property name (no prefix should be used here); possible superproperties are defined in the column `upper value` as a single term, or as a comma-separated list of terms. The domain and range classes can also be provided as a single term, or as a comma separated list thereof, in the `domain` and `range` columns. There should be a label and a longer description in English text, provided by the `label` and `comment` columns, respectively. The `see also` column may provide a set of extra links; these are translated into `rdfs:seeAlso` statements in the vocabulary.
  
    The `range` column may also use the (single) term `IRI` instead of class references. This keyword denotes a property that has no explicit range, but whose objects are expected to be IRI references. The generated vocabulary annotates these properties as belonging to the `owl:ObjectProperty` class, which is the term reserved for properties whose objects are not supposed to be literals. 
  
    A property can be declared as deprecated by setting its `deprecated` column to "yes".
- `individual`: definition of an individual, i.e., a single resource defined in the vocabulary. The `id` column defines the individual's name (no prefix should be used here); the possible types are defined in the column `upper value` as a single term, or a comma separated list of terms. There should be a label and a longer description in English text, provided by the `label` and `comment` columns, respectively. The `see also` column may provide a set of extra links; these are translated into `rdfs:seeAlso` statements in the vocabulary.

## Installation and use

The script is in TypeScript (version 4.6 and beyond) running on top of `node.js` (version 16 and beyond). Take the following steps to install and run the script:

1. Install [`node.js`](https://nodejs.org/) on your local machine. Installation of `node.js` should automatically install the [`npm`](https://www.npmjs.com) package manager.
2. Clone the repository to your local machine
3. In the directory of the repository clone, run `npm install` on the command line. This installs all the necessary packages in the `node_modules` subdirectory.
4. Modify the `main.ts` file's first line (staring with `#!`) to refer to the local file directory where the installation happened
5. Create a directory for the vocabulary definition; this should include
   1. A `vocabulary.csv` file. You can start with the CSV file in the `example` directory of the repository, and change the cells for your vocabulary.
   2. A `template.html` file. You can start with the HTML file in the `example` directory of the repository, and adapt/change it as you wish. Be careful with the changes, though: the script relies on the existing `id` values and section structures.
6. Run the `main.ts` file in the directory that contains the `vocabulary.csv` and `template.html` files

The vocabulary specification in CSV is in the `vocabulary.csv` file. The script generates the files `vocabulary.ttl`, `vocabulary.jsonld` and `vocabulary.html` for, respectively, the Turtle, JSON-LD, and HTML representations.

## Content of the directory

- `Readme.md`: this file.
- `vocabulary.csv`: the vocabulary specification.
- `package.json`: configuration file for `npm`.
- `template.json`: an HTML template file used by the script; it is the skeleton of the final HTML format, also based on [ReSpec](https://respec.org/docs/). If the file is modified, care should be taken to not change the core structure and the various, possibly empty, HTML elements with `@id` values. The script fills those elements with content when generating the `vocabulary.html` file.
- `lib` directory: the TypeScript modules for the script.
- `main.ts`: the TypeScript entry point to the script.
- `example` directory: example csv and template files.

The following files and directories are generated/modified by either the script or `npm`; better not to touch these directly:

- `package-lock.json`: used by `npm` as an internal file for the packages.
- `node_modules` directory: the various Javascript libraries used by the script. This directory should _not_ be uploaded to github, it is strictly for the local activation of the script.
- `example/vocabulary.ttl`, `example/vocabulary.jsonld`, `example/vocabulary.html`: the vocabulary representation in different formats, used for testing.

## Acknowledgement

The original idea, structure, and script (in Ruby) was created by Gregg Kellogg for v1 of the Credentials Vocabulary. The CSV definitions have been slightly updated/changed, and the script itself has been re-written in TypeScript.
