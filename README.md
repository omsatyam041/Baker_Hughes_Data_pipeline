Chemical Composition Table Extraction from Scanned PDF
1. Overview
This project extracts a chemical composition table from a scanned PDF certificate, where the table is embedded as an image rather than selectable text.
The extracted information is cleaned, structured, validated, and exported as a CSV file.
The solution works fully offline, uses only open-source libraries, and is designed to handle OCR noise, inconsistent formatting, and domain-specific constraints commonly found in material test certificates.

________________________________________
2. Setup & Run Instructions
2.1 Prerequisites
●	Python: 3.9+
●	Operating System: Windows / Linux / macOS
●	Tesseract OCR (installed locally)
Install Tesseract (Windows)
●	Download from: https://github.com/UB-Mannheim/tesseract/wiki
Install to default location:
C:\Program Files\Tesseract-OCR
	
________________________________________
2.2 Python Dependencies
Install required libraries:
pip install pdf2image pytesseract opencv-python pandas numpy pillow


________________________________________
2.3 Running the Project
1.	Place the input PDF in the working directory
2.	Open the Jupyter Notebook (or run the script)
3.	Execute cells in order:
○	PDF → Image conversion
○	Image preprocessing
○	OCR extraction
○	Table parsing
○	CSV exportt
4.  Final output:  chemical_composition.csv

________________________________________
3. Approach Overview
Step 1: PDF to Image Conversion
●	Each page of the PDF is converted into a 300 DPI image using pdf2image.
●	High resolution improves OCR accuracy on scanned documents.

________________________________________
Step 2: Image Preprocessing
●	Images are preprocessed using OpenCV:
○	Grayscale conversion
○	Gaussian blur for noise reduction
○	Adaptive thresholding for text enhancement
This step significantly improves OCR reliability.

________________________________________
Step 3: OCR Text Extraction
●	Tesseract OCR is applied to the cleaned image.
●	Output is expected to be noisy due to scan quality and formatting.

________________________________________
Step 4: Section Detection
●	Instead of processing all OCR text, extraction is anchored to the “Chemical Composition” section.
●	This avoids unrelated content such as addresses, standards, and inspection notes.

________________________________________
Step 5: Table Row Identification
●	Numeric-heavy rows (e.g., rows starting with BOTTOM) are identified as table data.
●	This mimics how humans visually identify composition rows in certificates.

________________________________________
Step 6: Data Cleaning & Normalization
●	Decimal commas (0,004) are converted to dots (0.004)
●	Threshold values (<0.001) are preserved
●	Numeric values are normalized for computation
●	A detection-limit flag is added for scientific correctness

________________________________________
Step 7: Chemical Validation
●	Titanium is specified in the certificate as “remainder”
●	Final validation computes:
●	Ti = 100 − sum(all other elements)
●	This ensures chemical completeness and correctness.


________________________________________
4. Design Decisions
●	Image-first approach: Required because tables are not vector text
●	Section anchoring: Reduces false positives from OCR noise
●	Domain-aware mapping: Element order follows Ti-6Al-4V standards
●	Preserve raw values: Ensures traceability to original certificate
●	Offline execution: Required by assignment constraints

________________________________________
5. Assumptions
●	The chemical composition follows standard Ti-6Al-4V alloy ordering
●	Threshold values (<) indicate detection limits, not exact values
●	Titanium is the base material unless explicitly stated otherwise
●	Input PDFs are readable scans (not severely corrupted)

________________________________________
6. Limitations
●	Extremely poor scan quality may reduce OCR accuracy
●	Multi-table PDFs require extending section detection logic
●	Element-value mapping relies on known alloy standards
●	Handwritten annotations are not supported

________________________________________
7. Known Edge Cases & Handling
Edge Case	Handling Strategy
OCR noise / garbled text	Image preprocessing + section anchoring
Decimal commas (0,004)	Normalized to decimal points
< threshold values	Preserved in raw_value, flagged separately
Base element as “remainder”	Computed mathematically
Extra numeric rows	Filtered using semantic cues (BOTTOM)
Partial tables	Extract only detected numeric rows

________________________________________
8. Output Format
The final CSV contains:
●	element_symbol
●	raw_value
●	value
●	unit
●	is_below_detection
Example:
element_symbol,raw_value,value,unit,is_below_detection
C,0.004,0.004,wt.%,False
Fe,<0.001,0.001,wt.%,True
Ti,remainder,99.7072,wt.%,False

________________________________________
9. Conclusion
This project demonstrates a real-world OCR pipeline that combines:
●	Image preprocessing
●	Robust OCR extraction
●	Domain-aware parsing
●	Scientific validation
The solution is reproducible, offline-friendly, and suitable for industrial use cases involving scanned material certificates.


