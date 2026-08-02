# SAVE-OWL: Sequence Analyzer & Viewer Engine on the Web (Lite)

This platform acts as an un-gated, client-side single-page application (SPA) designed for rapid batch sequence mutation analytics, population frequency compounding, and scalable vector graphics (SVG) generation. By implementing all algorithmic components directly in the browser runtime, it eliminates remote data transfers, preserving data privacy.

---

## How to Use

You can use this application instantly either via your web browser or completely offline on your local machine. No installation or registration is required.

### Option 1: Use the Web Application
* **Access URL**: [https://your-server.com](https://your-server.com)
* *100% Client-side runtime. Your sequence data is processed entirely within your browser and is never transmitted to any external server.*

### Option 2: Run Locally / Offline
Since this is a pure client-side SPA with zero dependencies, you can run it directly from your computer without setting up a local web server:
1. **Download** or clone this repository to your machine.
2. **Double-click** the `index.html` file to open it in any modern web browser (Chrome, Firefox, Edge, Safari, etc.).
3. *Everything runs instantly inside your browser sandbox, keeping your workflows fully functional even without an internet connection.*

---

## 1. Sequence Input Parsing & Core Caching Engine
The platform supports multiple selection and drag-and-drop file inputs in both standard and multi-FASTA formats. Upon ingestion, sequence records are automatically parsed and appended to the tracking system cache.

* **Batch Checkboxes**: Act as a master cache repository. Checking or un-checking names updates the downstream analysis scope options.
* **Textarea Editing (Critical Principle)**: The text boxes are the **single source of truth** for the alignment engine. Clicking *"Apply Selection to Textbox"* updates FASTA headers and sequence strings inside these text boxes. The backend algorithm will read and align exactly what is visible in these boxes when executed. Users can directly type, erase, or alter sequences inside these boxes before executing the analysis.

---

## 2. Automatic Reverse Complement Alignment
Clicking *"Run Batch Alignment"* starts the computation of a cross-pivot matrix between all items in the query box against all items in the reference box, using a native matrix-instantiated **Smith-Waterman (Local Alignment) algorithm**.

* **Strand Autodetection**: For each individual query sequence, the system computes alignment scores for both the forward and reverse complement (RC) orientations. The orientation yielding the higher score is automatically selected.
* **Reverse Complement Reporting Standardization**: To ensure accurate profiling and aggregation of mismatches based on the reference coordinate system, if the RC orientation yields the higher score, the system automatically replaces the query sequence with its RC counterpart. Consequently, all subsequent mismatch and gap identifications are reported relative to this replaced RC sequence.

---

## 3. Masking No-Coverage Regions
Any reference coordinates falling outside the aligned range (before the first aligned base or after the last aligned base) are masked with a light gray background labeled **No Alignment Coverage**. This visual aid allows you to immediately distinguish between a true sequence conservation and a simple lack of data coverage.

---

## 4. Mismatch Aggregation & Plotting
Clicking *"Count and Plot Mismatches"* analyzes mismatches within your selected reference gene or region. The tool automatically counts recurring mismatches across all samples and displays them in a stacked bar chart. 

Click *"Download SVG"* to download the chart data in the SVG format. Click *"Download Mismatch CSV"* to download the mismatch data and its codon translation impact (missense, nonsense, or silent). This feature helps you easily identify mismatch hotspots and view the distribution of mismatch types at each position.

---

## 5. Demo Data Guide & Important Analysis Note

The `demo/` folder contains real-world validation datasets designed to test the platform's multi-FASTA parsing, automatic reverse-complement alignment, and mismatch plotting capabilities.

### Included Files
* **`GFP_variants_cds.fa`**: A multi-FASTA reference file containing Coding Sequences (CDS) for various green fluorescent protein variants (including GFP, mVenus, etc.).
* **`pBS-35S-BFP-1_G10-90F_premix.txt`**
* **`pBS-35S-BFP-2_G10-90F_premix.txt`**
* **`pBS-35S-BFP-3_G10-90_RC_premix.txt`**
  * Sanger sequencing reads (Sanger trace data exported as text) of Blue Fluorescent Protein (**BFP**) constructs. The BFP coding sequences (CDSs) were generated via **overlap extension PCR mutagenesis** using **mVenus** as the template, and subsequently cloned into the pBS-35SMCS-GFP vector, replacing the GFP CDS. 
  * Comparing these (as queries) against **mVenus** in `GFP_variants_cds.fa` (as a reference) will detect intentionally introduced mutations at positions **192, 195, 196, 198, and 199** of the mVenus sequence. The BFP-2 and BFP-3 sequences will exhibit additional mismatches, representing PCR errors introduced during cloning.