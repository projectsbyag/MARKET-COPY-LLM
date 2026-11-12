# Market Copy LLM

**Market Copy LLM** is a self-improving marketing copywriter built in Python and presented through a Gradio interface. It helps turn a product brief into landing-page copy by testing headline angles, drafting persuasive sections, simulating a skeptical customer review, and revising weak areas until the copy reaches a configurable quality goal.

The project is designed as an experiment in combining local language models, retrieval-augmented prompting, structured evaluation, and iterative refinement in one practical workflow.

## What it does

1. **Tests three headline angles** — pain, outcome, and contrarian — then selects the strongest option.
2. **Drafts landing-page copy** using a problem-agitate-solution-style structure with sections for the hook, mechanism, proof, and call to action.
3. **Retrieves relevant swipe-file examples** with `all-MiniLM-L6-v2` embeddings and a FAISS inner-product index. The examples guide rhythm and structure without copying product details.
4. **Acts as a skeptical buyer** and evaluates the draft for clarity, believability, repetition, flow, weak hooks, hype words, audience focus, and sentence length.
5. **Revises only when needed**, preserving the best-scoring draft and stopping early when the quality threshold is reached.
6. **Exports results** as Markdown, JSON review history, a FAISS index, and a clean PDF from the Gradio workflow.

## Screenshots

### Product brief and generated copy

![Market Copy LLM generated copy interface](./Opera%20Snapshot_2026-11-12_230651_c2d7bd01ec2d28f50d.gradio.live.png)

### Review and refinement workflow

![Market Copy LLM review interface](./Opera%20Snapshot_2026-11-12_230826_c2d7bd01ec2d28f50d.gradio.live.png)

## Workflow

```text
Product brief
     |
     v
Headline tournament (pain / outcome / contrarian)
     |
     v
Landing-page draft + retrieved swipe examples
     |
     v
Simulated skeptical-buyer review
     |
     +---- quality goal not met ----> targeted revision
     |
     v
Best final copy + score history + PDF export
```

## Architecture

- **Generation:** `Qwen/Qwen2.5-7B-Instruct` loaded with Transformers and 4-bit quantization through BitsAndBytes. The notebook also includes Mistral Nemo as an alternative model.
- **Retrieval:** `SentenceTransformer("all-MiniLM-L6-v2")` creates normalized embeddings for the marketing swipe file; FAISS searches for the most relevant examples.
- **Structured outputs:** JSON-oriented prompts and retry logic keep headline variants and buyer critiques machine-readable.
- **Quality loop:** the buyer review combines LLM feedback with lightweight copy scans, including hype-word counts, pronoun balance, and average sentence length.
- **Interface:** Gradio collects the brief, streams progress, displays final copy and review notes, and exposes the generated PDF for download.
- **Persistence:** optional Google Drive output stores the final Markdown, round-by-round JSON history, swipe index, and source examples.

## Input brief

The interface accepts:

- Product or service name
- Target audience
- Core customer pain
- How the product works
- Proof metric
- Call to action
- Maximum revision rounds
- Target quality score

The included example uses **LedgerSnap**, a receipt and invoice workflow for freelance developers and solo agency owners.

## Running the notebook

The project is provided as `Market_Copy_LLM.ipynb` and is suitable for Google Colab or another CUDA-enabled Jupyter environment.

```bash
pip install transformers accelerate bitsandbytes sentence-transformers faiss-cpu pypdf gradio fpdf2
```

Then run the notebook cells in order:

1. Install dependencies and load the quantized language model.
2. Initialize the embedding model and FAISS swipe-file index.
3. Define the drafting, headline, critique, revision, and export functions.
4. Launch the Gradio interface.

A GPU with enough VRAM for the selected quantized model is recommended. CPU-only execution may be possible with a smaller model, but generation will be substantially slower.

## Example output

For the included LedgerSnap brief, the system produces a headline such as:

> Save 4.5 hours/month with LedgerSnap.

It then expands the idea into a landing page with a hook and problem section, a cost-of-inaction and mechanism section, proof-oriented copy, and a clear call to action. Each review round records a score and the buyer's specific objections so the changes remain inspectable.

## Project structure

```text
.
├── Market_Copy_LLM.ipynb
├── Opera Snapshot_...230651...png
├── Opera Snapshot_...230826...png
└── README.md
```

## Limitations

- The quality score is an automated heuristic, not a substitute for real conversion data or user research.
- The swipe file is intentionally small and should be expanded with licensed, high-quality examples for production use.
- Generated claims and proof metrics should be reviewed by a human before publication.
- The notebook currently assumes access to a suitable model runtime and does not provide a production API server.

## Technology

Python · PyTorch · Transformers · BitsAndBytes · Sentence Transformers · FAISS · Gradio · FPDF2 · Google Drive

## License

No license has been specified yet. Add a license before redistributing the project.
