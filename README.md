# Land Matrix ResourceContracts Feeder

Export mining deals from ResourceContracts to Land Matrix.

---

## 📄 Information Extraction from Mining Contracts (Scanned PDFs)

This project automates the extraction of key data from scanned mining contract PDFs, including:

- Contract title  
- Country  
- Company  
- Resources  

It uses OCR, Named Entity Recognition (NER), and fuzzy matching, combining:

- **GROBID** for OCR and text structuring (via TEI XML)  
- **CamemBERT** for NER (pretrained French model)  
- **RapidFuzz** for company name resolution  

---

## 🛠 Technologies Used

- Python 3.x  
- [GROBID](https://github.com/kermitt2/grobid) (via Docker)  
- [CamemBERT NER](https://huggingface.co/Jean-Baptiste/camembert-ner)  
- [RapidFuzz](https://github.com/maxbachmann/RapidFuzz)  
- PyMuPDF (`fitz`)  
- BeautifulSoup / Regex  


## 🚀 Getting Started

### 1. Set up GROBID (Docker)

Make sure [Docker Desktop](https://www.docker.com/products/docker-desktop) is installed and running on your machine.

Run the following command in your terminal to launch GROBID:

```bash
docker pull lfoppiano/grobid:0.8.2
docker run --rm --init --ulimit core=0 -p 8070:8070 lfoppiano/grobid:0.8.2
```

### 2. Set up the Python Environment

Open a terminal (e.g. in VS Code):

```bash
python -m venv venv
.\venv\Scripts\activate     # Windows
pip install -r requirements.txt
```
If you're running from a Jupyter notebook, just make sure you have the required libraries installed (transformers, fitz, bs4, requests, rapidfuzz, etc.).

### 3. Organize Input Data

You will need the following input files and folders:

- A folder with PDF mining contracts
- A list of country names (`countries_fr.txt`)
- A list of natural resources (`resources.txt`)
- A list of company names (`societes.txt`)

These files are provided via Nextcloud (ask the project owner for access).

Update the paths in the script or notebook to match your local setup:

```python
PDF_FOLDER = "path/to/pdf_folder"
COUNTRY_LIST_FILE = "path/to/countries_fr.txt"
RESOURCE_LIST_FILE = "path/to/resources.txt"
COMPANY_LIST_FILE = "path/to/societes.txt"
```
### 4. Run the Extraction Pipeline

You can run the full extraction via the provided **Jupyter notebooks**, located in the `src/notebooks/` or `src/scripts/` folder.

The main steps include:

- OCR and text extraction from scanned PDFs
- Named Entity Recognition (NER) using CamemBERT
- Extraction of key entities (title, country, company, resource)

Running the notebooks will process the PDF contracts and export a `combined_extraction_output.json` file containing the extracted information.

## 🧠 Extraction Logic

The pipeline includes:

- OCR (first page via PyMuPDF + full document via GROBID XML)
- Heuristic title extraction using keywords
- Named Entity Recognition (NER) for locations and organizations
- Fuzzy matching against a known list of companies
- Keyword detection for natural resources


## 📁 Output

Each PDF is processed into a JSON object with the following structure:

```json
{
  "id": "1",
  "fichier": "1.pdf",
  "titre_contrat": "Mining Agreement between X and Y",
  "pays_cible": ["congo", "rdc"],
  "ressources": ["copper", "gold"],
  "societe_exploitation": "Glencore",
  "entites_principales": {
    "pays": [...],
    "societes_reconnues": {...},
    "autres_orgs": {...},
    "societes_non_matchées": [...]
  }
}
```

## ✅ Notes

- The model is optimized for French-language scanned contracts.  
- OCR performance may vary depending on PDF quality.  
- CamemBERT NER can miss multi-word organizations or noisy entities — heuristics and fuzzy matching help mitigate this.
