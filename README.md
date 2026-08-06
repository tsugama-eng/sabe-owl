# SAVE-OWL: Sequence Analyzer & Viewer Engine on the Web (Lite)

This platform acts as an un-gated, client-side single-page application (SPA) designed for rapid batch sequence mutation analytics, population frequency compounding, and scalable vector graphics (SVG) generation. By implementing all algorithmic components directly in the browser runtime, it eliminates remote data transfers, preserving data privacy.

---

## How to Use

You can use this application instantly either via your web browser or completely offline on your local machine. No installation or registration is required.

### Option 1: Use the Web Application
* **Access URL**: [https://webpark2116.sakura.ne.jp/save-owl/]
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

## 5. Wave-Driven Phase Deconvolution & Reference-Centric Multiple Sequence Alignment (MSA)
Checking *"Enable Heterozygous Phase Analysis"* and clicking *"Run Batch Alignment"* initiates a specialized dual-allele deconvolution pipeline designed to analyze complex heterozygous frameshift mutations (such as CRISPR-Cas9-induced indels) directly from raw chromatogram trace data.

*   **Anchor Sequence Selection**: Users must input a clean, homozygous sequence block flanking the editing site (e.g., 15–25 bp) that is entirely free of mismatches. This acts as the primary coordinate probe.
*   **Bidirectional Slidewindow Probing**: Based on the automated forward/reverse strand validation of your `.ab1` file, the system dynamically sweeps an internal 20-mer sliding k-mer probe (secondary probe) upstream (for reverse complement orientations) or downstream (for forward orientations) to calculate the precise structural boundaries where trace harmony ruptures and returns.
*   **Dual-Allele Unmixing**: Once the phase rupture boundaries are locked, the engine unmixes the overlapping chromatogram traces, isolating individual target variants into distinct **Allele A** and **Allele B** sequence tracks.
*   **Reference-Centric Grid Viewer**: Clicking *"Generate Alignment"* bypasses traditional gap-scattering progressive algorithms. It projects all isolated alleles onto a unified, absolute reference coordinate system. Samples possessing mutations or insertions relative to the reference sequence flag their track names in **bold red labels**, while perfect matches remain muted.

---

## 6. Demo Data Guide & Important Analysis Note

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

To evaluate SAVE-OWL's unique wave-driven phase deconvolution and absolute-coordinate projection capabilities, execute the following workflow using the provided Cane Toad demo files:

1. **Set the Reference Framework**: Copy and paste the target gene's wild-type sequence ("fwd_control.ab1" or "rev_control.ab1") into the **Reference Sequence(s)** input field.
2. **Load Raw Chromatograms**: Drag and drop the `.ab1` trace files from the `Clark_2025_zenodo_cane_toad_CRISPR-Cas9_ab1_files/` directory into the query ingestion pane.
3. **Configure the Dual-Probe Parameters**:
   * Check the ***"Enable Heterozygous Phase Analysis"*** box to unlock the deconvolution engine.
   * **Anchor Sequence Input**: Paste a clean, homozygous sequence block flanking the targeted cleavage site (e.g., a 20 bp sequence immediately upstream/preceding the expected cut point on your forward reference). *Ensure this specific text string is entirely free of mismatches.*
   * **Secondary Anchor Search Offset (bp)**: Set this field to your desired buffering padding size (the baseline default value is `20`). This establishes the gap spacing before the system deploys the *20-mer sliding k-mer probe (secondary probe)*.
4. **Execute Deconvolution**: Click ***"Run Batch Alignment"***. The background algorithm will automatically scan for file orientations, dynamically mirror the secondary probe's sliding track direction (upstream for RC tracks, downstream for Forward tracks), locate the realignment boundary, and instantly unmix the traces into independent **Allele A** and **Allele B** vectors.
5. **Visualize the Standardized Matrix**: Scroll up/down and click ***"Generate Multiple Sequence Alignment (MSA)"***. The framework will display your crisp, dark-themed reference-centric matrix view. Notice how mutated or frame-shifted allele variants are aligned to coordinates, flagging their tracking names in **red labels** while wild-type tracks remain muted.

---

## 7. Troubleshooting & Verification Best Practices (Critical Alignment Principles)

Because Sanger trace deconvolution relies on locating automated mathematical boundaries, suboptimal user parameters can occasionally lead to misleading matrix outputs rather than hard errors. Always adhere to the following two validation rules:

1. **Verify Mismatches via Pairwise Alignment**: Before trusting the heterozygous phasing results, it is highly recommended to run the pairwise alignment first to map out exactly where the mutations and mismatches are located. The anchor sequences and search positions for the phase analysis should then be chosen strategically so that they securely flank the specific region containing those identified mismatches.
2. **Handle Large Indels by Widening the Secondary Offset**: The *Secondary Anchor Search Offset* determines how much "breathing room" the software skips over the mutation site before initiating the 20-mer secondary probe scan. 
   * If your editing experiment induced a **large insertion clone (e.g., >20 bp inserts)** or a massive deletion, a narrow default offset (like 20 bp) will trap the sliding probe *inside* the disrupted mutation territory. This causes the probe to either fail or lock onto a false repetitive sequence motif.
   * **The Solution**: If a sample exhibits complex, large-scale variations or returns an irregular layout, simply increase the **Secondary Anchor Search Offset to 40, 50, or 60 bp**. This forces the secondary probe to jump completely past the messy variant zone, securely sandwiching the mutation between clean, homozygous flanking boundaries.
