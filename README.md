# SABE-OWL: Sequence Analyzer & Viewer Engine on the Web (Lite)

This platform acts as an un-gated, client-side single-page application (SPA) designed for rapid batch sequence mutation analytics, population frequency compounding, and scalable vector graphics (SVG) generation. By implementing all algorithmic components directly in the browser runtime, it eliminates remote data transfers, preserving data privacy.

---

## How to Use

You can use this application instantly either via your web browser or completely offline on your local machine. No installation or registration is required.

### Option 1: Use the Web Application
* **Access URL**: [https://tsugama-eng.github.io/sabe-owl/] or [https://webpark2116.sakura.ne.jp/sabe-owl/]
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

## 5. Trace-Faithful Phase Deconvolution Engine
Clicking *"Run Phasing"* initiates a specialized, high-fidelity dual-allele deconvolution pipeline designed to resolve overlapping chromatogram tracks (such as biallelic point mutations, fixed substitutions, or block variations) directly from raw ABI file metrics.

*   **Textbox Routing Constraint (Single Reference Rule)**: The phasing engine uses **only the first (top) sequence** of the Reference Sequence(s) textarea to maintain a completely uniform absolute baseline coordinate system.
*   **Direct Trace Extraction & Tail Auto-Recovery**: The engine directly extracts the raw 4-channel wave data and official base-call positions from your loaded ABI file(s). The algorithm automatically calculates the baseline spacing intervals to restore and complete the trailing peak matrix if a trace cuts off prematurely.
*   **Localized Peak-Search & Noise Correction**: For every individual base position, the engine automatically scans a local window of *5* sampling points across all four color channels (A, C, G, T), locking onto the true peak maximum to cleanly neutralize trace shifts, background baseline variations, and raw dye-blob anomalies.
*   **Dynamic User Control Parameter Panel**:
    *   *Start Position (QPos)*: Overrides the automatic slope detection to force the phasing sequence origin to an absolute coordinate index.
    *   *Specific Anchor Sequence & Query RC*: Forces the phasing sequence origin to the provided sequence (e.g., a sequence immediately upstream of CRISPR-Cas9 guide RNA) and automatically determines whether to deconvolve in the forward direction or execute a Reverse Complement strand calculation matrix inversion.
    *   *Regex Window Width*: Determines the size of the sliding regular-expression matching window (default 20 bp) to balance mapping sensitivity against sequence complexity.
    *   *Secondary Peak Ratio*: Calibrates the software's sensitivity to minor signal heights (default 0.12). True secondary traces above this ratio are processed as polymorphic variants, while signals below are filtered out as clean background noise.

---

## 6. One-to-One Peak Allocation & Biallelic Batch Multiple Sequence Alignment (MSA)
Once coordinates are locked, the engine decodes overlapping peak signals under a strict rule of 1-to-1 trace faithfulness.

*   **Mutual Trace Complementarity**: The engine splits dual-peak traces into Allele A and Allele B by cross-comparing them with the reference sequence template. If a position is clean or lacks a secondary trace, both alleles receive identical base assignments, ensuring zero data loss or artificial mutations.
*   **Co-linear Equality**: Allele A and Allele B are constructed to be perfectly equal in length, acting as an un-gapped, 1-to-1 reflection of the physical trace peaks. These pure allele sequences are used for downstream alignments.
*   **Consolidated Dashboard Visualization**: The engine automatically hides the legacy aggregator and displays the **Biallelic Batch Multiple Sequence Alignment Panel**, locking all sample tracks horizontally under a single global tracking index.
*   **Data Export**: Click *"Download FASTA"* or *"Download Alignment (.txt)"* to generate instant data packages compiled via clean, client-side Blob object allocation.

---

## 7. Demo Data Guide & Important Analysis Note

The `demo/` folder contains real-world validation datasets designed to test the platform's multi-FASTA parsing, automatic reverse-complement alignment, and mismatch plotting capabilities.

### Included Files
* **`GFP_variants_cds.fa`**: A multi-FASTA reference file containing Coding Sequences (CDS) for various green fluorescent protein variants (including GFP, mVenus, etc.).
* **`pBS-35S-BFP-1_G10-90F_premix.txt`**
* **`pBS-35S-BFP-2_G10-90F_premix.txt`**
* **`pBS-35S-BFP-3_G10-90_RC_premix.txt`**
  * Sanger sequencing reads (Sanger trace data exported as text) of Blue Fluorescent Protein (**BFP**) constructs. The BFP coding sequences (CDSs) were generated via **overlap extension PCR mutagenesis** using **mVenus** as the template, and subsequently cloned into the pBS-35SMCS-GFP vector, replacing the GFP CDS. 
  * Comparing these (as queries) against **mVenus** in `GFP_variants_cds.fa` (as a reference) will detect intentionally introduced mutations at positions **192, 195, 196, 198, and 199** of the mVenus sequence. The BFP-2 and BFP-3 sequences will exhibit additional mismatches, representing PCR errors introduced during cloning.

* **`Clark_2025_zenodo_cane_toad_CRISPR-Cas9_ab1_files/`** (Directory):
  * A real-world validation dataset containing raw binary chromatogram (`.ab1`) files from a CRISPR-Cas9 genome editing experiment targeting the *Rhinella marina* (Cane Toad) genome. 
  * **Data Source & Attribution**: This dataset is sourced from the public repository Zenodo (Clark, 2025, https://zenodo.org/records/15945185) under a Creative Commons Attribution 4.0 International (CC-BY 4.0) license. A formal data description and associated biological study are available in the peer-reviewed article (Clark et al., 2025, https://doi.org/10.1177/25731599251382427).

  * **Testing Orientation Flexibility**: This folder contains true heterozygous mixed-trace files sequenced from both **Forward** and **Reverse Complement (RC)** strand configurations. 

---

### Step-by-Step Heterozygous Phase Analysis Tutorial

To evaluate SABE-OWL's unique wave-driven phase deconvolution and absolute-coordinate projection capabilities, execute the following workflow using the provided Cane Toad demo files:

1. **Set the Reference Framework**: Copy and paste the target gene's wild-type sequence ("fwd_control.ab1" or "rev_control.ab1") into the **Reference Sequence(s)** input field.
2. **Load Raw Chromatograms**: Drag and drop the `.ab1` trace files from the `Clark_2025_zenodo_cane_toad_CRISPR-Cas9_ab1_files/` directory into the query ingestion pane.
3. **Configure the Phasing Advanced Parameters**:
   * **1. Start Position (QPos)**: Leave blank to use the auto-detection. If you already know the exact base index where the heterozygous indel mutation begins, enter that number (or any) here to force an absolute starting coordinate override.
   * **2. Specific Anchor Sequence & Query RC**: Paste a clean, homozygous sequence (e.g., a 20 bp sequence immediately upstream/preceding the expected Cas9 cut point) present in the query sequence(s). The engine will automatically scan both strands for this tag, auto-lock the orientation (or force it to RC if *Query RC* is checked), and set the phasing origin to the very next character, bypassing automatic estimation.
   * **3. Regex Window Width (bp)**: Leave blank to use the system default of `20`. This determines the sliding regular-expression window width used to maintain phase tracking down the remainder of the overlapping trace sequence.
   * **4. Secondary Peak Ratio**: Leave blank to use the baseline default of `0.12` (12%). Adjust this value lower to capture faint underlying secondary signals, or higher to clean up heavy background noise.
4. **Execute Deconvolution**: Click ***"Run Phasing"***. The background algorithm will automatically scan for file orientations according to the provided reference sequence and unmix the traces into **Allele A** and **Allele B** sequences. Multiple and individual sequence alignments are then displayed.


---

## 8. Troubleshooting & Verification Best Practices (Critical Alignment Principles)

Because Sanger trace deconvolution relies on locating automated mathematical boundaries, suboptimal user parameters can occasionally lead to misleading matrix outputs rather than hard errors. Always adhere to the following two validation rules:

1. **Verify Mismatches via Pairwise Alignment**: Before trusting the heterozygous phasing results, it is highly recommended to run the pairwise alignment first to map out exactly where the mutations and mismatches are located. The anchor sequences and search positions for the phase analysis should then be chosen strategically so that they securely flank the specific region containing those identified mismatches.
2. **Optimize via Iterative Testing (The "Push & Compare" Workflow)**: Perfect coordinates may not be obtained on your first try.
   * **The Dynamic Behavior**: Whether a file triggers an automatic orientation flip or is forced via the *Query RC* checkbox, the backend handles the sequence inversion instantly. The best way to verify your data would be to test different parameters fluidly.
   * **The Best Practice**: Simply adjust your variables (such as shifting the *1. Start Position*, tweaking the *4. Secondary Peak Ratio*, or toggling *Query RC*) and click ***"Run Phasing"*** multiple times. Inspect both the individual **pairwise alignment blocks** and the global **Biallelic Batch Multiple Sequence Alignment Panel**. When the alignments are long and clean, the phase-lock parameters would be optimal.
