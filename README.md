# Talmud Named Entity Recognition (NER) System

A comprehensive system for extracting and analyzing names and entities in Talmudic literature, based on the Steinsaltz Talmud translation.

## Overview

This project implements a Named Entity Recognition (NER) system specifically designed for Talmudic text analysis, focusing on onomastics (the study of names) in Hebrew and Jewish Aramaic in Late Antiquity. The system can identify various categories of entities including Tannaim, Amoraim, honorifics, patronymics, toponyms, and more.

## Features

- **Custom NER Model**: Trained on Talmudic text to recognize specific entity types
- **Pattern Matching**: Regex-based detection of complex name patterns
- **Entity Database**: Searchable concordance of identified entities
- **Network Analysis**: Visualize relationships between Talmudic sages
- **Reference Data**: Integration with known information about Talmudic figures
- **Web Interface**: Simple exploration of extracted entities

## Getting Started

### Prerequisites

- Google account (for Google Colab and Google Drive)
- Steinsaltz Talmud text file

### Setup Instructions

1. **Open in Google Colab**:
   - Go to [Google Colab](https://colab.research.google.com/)
   - Create a new notebook or upload the provided `talmud_ner_colab_notebook.ipynb`

2. **Prepare Your Text File**:
   - Upload your Steinsaltz Talmud text file to Google Drive
   - Note the path to your file

3. **Set Up Output Directory**:
   - Create a folder in your Google Drive for output files (e.g., `talmud_ner_output`)

4. **Run the Notebook**:
   - Update the `talmud_file_path` variable to point to your text file
   - Run all cells in the notebook

## System Components

### 1. Entity Categories

The system recognizes the following entity types:

- `PERSON`: General person names
- `TANNA`: Tannaim (Mishnaic sages)
- `AMORA`: Amoraim (Talmudic sages)
- `HONORIFIC`: Titles like "Abba", "Rabbi", "Mar"
- `PATRONYMIC`: Names derived from father (ben/bar X)
- `MATRONYMIC`: Names derived from mother
- `TOPONYM`: Place names or location-based surnames
- `OCCUPATION`: Occupation-based identifiers
- `EPITHET`: Descriptive nicknames or attributes
- `GROUP`: References to groups of people
- `PLACEHOLDER`: Placeholder names used in hypothetical discussions

### 2. Name Pattern Recognition

The system implements pattern matching for complex name structures:

- Honorific + Name: "Rabbi Akiva"
- Patronymic: "Yehuda ben Beteira"
- Toponymic: "Yochanan of Tiberias"
- Combined patterns: "Rabbi Yehuda ben Beteira from Jerusalem"
- Special forms: teknonyms, epithets, titles

### 3. Network Analysis

The system creates network visualizations showing:

- Connections between sages mentioned together in the same context
- Hierarchical relationships between teachers and students
- Geographical connections between sages and locations

### 4. Entity Database

The entity concordance provides:

- Comprehensive list of all identified entities
- Contextual information for each entity
- Reference data integration
- Frequency analysis

## Advanced Usage

### Improving the Model

1. **Manual Annotation**:
   - Use the annotation sample to manually identify entities
   - Create a more comprehensive training dataset
   - Retrain the model using the expanded dataset

2. **Pattern Customization**:
   - Add or modify regex patterns in the `NAME_PATTERNS` dictionary
   - Create custom patterns for specific research questions
   - Fine-tune pattern matching for increased accuracy

3. **Reference Data Enhancement**:
   - Add more entries to the `talmudic_figures` and `talmudic_places` dictionaries
   - Incorporate data from external resources like Sefaria or academic publications
   - Link identified entities to biographical databases

### Processing the Full Corpus

To process the entire Talmud text:

1. Modify the `sample_size` variable in the extract_entities_from_corpus function call:
   ```python
   entities_df = extract_entities_from_corpus(trained_model, talmud_sentences)  # No sample_size limit
   ```

2. Increase training iterations for a more robust model:
   ```python
   trained_model = train_ner_model(nlp, INITIAL_TRAINING_DATA, iterations=100)
   ```

3. Run the processing in batches if needed, for very large texts.

## Integration with Other Resources

### Sefaria Integration

The system can be extended to work with Sefaria's open-access corpus:

1. Use the Sefaria API to fetch text data
2. Process and analyze the additional text
3. Cross-reference entities with Sefaria's metadata

Example code for Sefaria API integration:

```python
import requests

def get_sefaria_text(tractate, chapter=None):
    base_url = "https://www.sefaria.org/api/texts/"
    
    # Get specific chapter or full tractate
    if chapter:
        url = f"{base_url}{tractate}.{chapter}"
    else:
        url = f"{base_url}{tractate}"
    
    response = requests.get(url)
    return response.json()

# Example: Get Berakhot chapter 1
berakhot_1 = get_sefaria_text("Berakhot", 1)
```

### Academic Database Connection

To integrate with academic databases like those mentioned in your prospectus:

1. Import data from "Introduction to Personalities in Rabbinic Literature" (Katz)
2. Connect to "The Rabbinic Citation Network" data
3. Link with "SageBook" prosopographical data

## Advanced Analysis Techniques

### Generational Analysis

Study how terminology and name patterns evolve across generations:

```python
# Group entities by generation
generation_groups = enriched_entities[enriched_entities['generation'].notnull()]
generation_groups = generation_groups.groupby('generation')

# Analyze patterns by generation
for generation, group in generation_groups:
    print(f"Generation {generation}: {len(group)} entities")
    print(f"Most common entity types: {group['label'].value_counts().head()}")
```

### Geographic Distribution

Analyze the distribution of sages by region:

```python
# Plot distribution by location
location_counts = enriched_entities['location'].value_counts()
plt.figure(figsize=(10, 6))
sns.barplot(x=location_counts.index, y=location_counts.values)
plt.title('Distribution of Sages by Location')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

### Honorific Analysis

Study the usage patterns of honorifics:

```python
# Extract honorifics using pattern matching
honorifics = []
for text in talmud_sentences:
    matches = re.finditer(r'(Rabbi|Rabban|Rav|Mar|Abba)\s+([A-Z][a-z]+)', text)
    for match in matches:
        honorifics.append(match.group(1))

# Analyze honorific distribution
honorific_counts = Counter(honorifics)
print(honorific_counts)
```

## Building a Web Application

For a more comprehensive web interface:

1. Use Flask to create a backend API:
   ```python
   from flask import Flask, request, jsonify, render_template
   
   app = Flask(__name__)
   
   @app.route('/')
   def home():
       return render_template('index.html')
   
   @app.route('/api/extract', methods=['POST'])
   def extract_entities():
       text = request.json.get('text', '')
       doc = nlp(text)
       entities = [{'text': ent.text, 'label': ent.label_, 
                   'start': ent.start_char, 'end': ent.end_char} 
                  for ent in doc.ents]
       return jsonify({'entities': entities})
   
   if __name__ == '__main__':
       app.run(debug=True)
   ```

2. Deploy to a hosting service like Heroku, Google Cloud, or PythonAnywhere.

## Troubleshooting

### Common Issues

1. **Low Entity Recognition Rate**
   - Expand training data with more examples
   - Adjust pattern matching expressions
   - Ensure text is properly preprocessed

2. **Incorrect Entity Classifications**
   - Add specific examples to the training data
   - Check for overlapping patterns
   - Create rules for disambiguation

3. **Memory Issues with Large Texts**
   - Process text in batches
   - Use streaming approaches for very large corpuses
   - Consider distributed processing for extremely large datasets

## Future Enhancements

Potential improvements for future development:

1. **Hebrew/Aramaic Support**: Extend the model to work with original language texts
2. **Timeline Visualization**: Create temporal visualizations of sages and their interactions
3. **Auto-update Mechanism**: Create a system to periodically retrain the model
4. **Cross-reference with External Databases**: Connect entities with biographical databases
5. **Contextual Understanding**: Develop a system to understand relationships between entities

## References

1. Menachem Katz, "Introduction to Personalities in Rabbinic Literature" (2005)
2. Michael Satlow et al., "The Rabbinic Citation Network", AJS Review (2020)
3. Maayan Zhitomirsky-Geffet et al., "SageBook: toward a cross-generational social network for the Jewish sages' prosopography"
4. Josh Waxman, "A graph database of scholastic relationships in the Babylonian Talmud"

## License

This project is available for academic and research purposes.

## Acknowledgments

- Based on the research prospectus for a Large Language Model to facilitate Talmud research
- Uses open-source tools including spaCy, NetworkX, and Pandas
- Inspired by digital humanities approaches to ancient texts

---

For questions, suggestions, or collaboration opportunities, please reach out to the project maintainer.
