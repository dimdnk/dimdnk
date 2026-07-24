# Venue Intelligence Platform — Intelligence Layer & Competitive Landscape

> Technical and strategic reference for the document intelligence, ETL pipeline,
> and competitive positioning of VIP.

---

## 1. Competitive Landscape

### 1.1 Venue & Event Management Platforms

These are the established players. None of them are *document intelligence* platforms — they are operational booking/CRM systems that have started bolting AI on top of structured data they already own.

---

#### Cvent
**What it is:** The largest enterprise event management platform. 340K+ venues in its supplier network.

**Strengths:**
- Massive venue database (supply-side moat)
- RFP automation (send a brief, receive structured bids)
- AI-powered venue sourcing and recommendation
- Full event lifecycle: sourcing → registration → onsite → reporting
- Global footprint, strong enterprise contracts

**Gaps relevant to VIP:**
- Cvent is a *discovery and booking* platform — it doesn't help you manage your own venue library
- Venues in Cvent are self-submitted by venue owners, not extracted from your own documents
- No document intelligence (no PDF/floor plan/CAD parsing)
- No team-owned venue knowledge base
- Enterprise pricing puts it out of reach for SMB agencies

**Verdict:** Not a direct competitor. Cvent is a venue marketplace. VIP is an intelligence layer for your own venue portfolio. They could be *complementary* (import discovered venues from Cvent into VIP).

---

#### Tripleseat
**What it is:** Sales and catering software for restaurants, hotels, and unique venues. 20,000+ venue clients.

**Strengths:**
- Strong operational workflows (booking, contracts, invoicing)
- Just launched "Tripleseat Intelligence" — AI suite built on their dataset of millions of events
- AI for: demand forecasting, F&B inventory recommendations, conversational analytics, peer benchmarking
- Deeply embedded in hospitality operations

**Gaps relevant to VIP:**
- Tripleseat is built *for venues* to manage their events — not *for planners* to manage their venue portfolio
- Their AI runs on their own transactional data (bookings), not on unstructured documents
- No document parsing, no cross-venue search for planners
- No support for planner's own uploaded assets

**Verdict:** Different side of the market. Tripleseat serves venues; VIP serves planners. The intelligence architectures are fundamentally different: Tripleseat mines structured operational data; VIP mines unstructured documents.

---

#### Momentus Technologies (formerly Ungerboeck / VenueOps)
**What it is:** Enterprise-grade venue and event management. Serves convention centers, performing arts, stadiums, universities. 700+ performing arts centers, 50+ countries.

**Strengths:**
- End-to-end: booking → operations → finance → analytics
- AI-powered platform enhancements (Feb 2026): operational insights, space optimization
- 20+ years of venue and event intelligence baked into their models
- WeTrack product for safety/sustainability/risk management

**Gaps relevant to VIP:**
- Heavy enterprise product, not accessible to SMB agencies
- Focused on venue operators managing their own space, not planners curating a portfolio
- No document intelligence or ETL pipeline
- Implementation takes months, not minutes

**Verdict:** Enterprise venue ops software. No overlap with VIP's document intelligence core.

---

#### Perfect Venue / Planning Pod / Event Temple
**What it is:** Lightweight venue management tools targeting independent venues, small hotels, wineries.

**Strengths:** Affordable, easy to set up, covers booking basics.

**Gaps:** No AI, no document intelligence, no team venue library concept. More CRM than intelligence platform.

**Verdict:** Irrelevant to VIP's positioning. Different price/feature tier entirely.

---

### 1.2 Document Intelligence & ETL Platforms

These are the infrastructure players. They are the technical substrate that VIP's pipeline either competes with or can leverage.

---

#### Unstructured.io
**What it is:** The leading enterprise ETL platform for unstructured data. Purpose-built to prepare documents for LLMs and RAG pipelines.

**What it does:**
- Ingests: PDFs, Word, PowerPoint, HTML, emails, images, scanned docs
- Outputs: clean, chunked, LLM-ready JSON
- Connectors: S3, SharePoint, Google Drive, Confluence, 25+ sources
- Destinations: vector databases, data warehouses
- Handles: OCR, table extraction, layout detection, multi-column PDFs

**Pricing:** Free tier (15K pages, no expiry). Pay-as-you-go (~$2.66/compute hour). Enterprise subscription.

**Relevance to VIP:**
- Unstructured.io is what VIP's ETL layer *could use as a backend* rather than building from scratch
- Their open-source library (`unstructured`) can be self-hosted
- Handles the hardest parsing problems (scanned PDFs, multi-column layouts, tables)
- Not a product for end users — pure infrastructure/API

**Strategic insight:** VIP doesn't need to reinvent document parsing. Unstructured.io (or Docling) handles the extraction layer. VIP's value add is the *venue-specific intelligence* on top — the domain schema, the aggregation model, the search experience, the team collaboration.

---

#### IBM Docling
**What it is:** Open-source document conversion toolkit from IBM Research Zurich, now under Linux Foundation AI & Data.

**What it does:**
- State-of-the-art PDF layout analysis (DocLayNet model)
- Table structure recognition (TableFormer model)
- OCR for scanned documents
- Exports: clean Markdown, structured JSON with full metadata
- Handles: PDFs, DOCX, PPTX, HTML, images
- Runs locally, no cloud dependency, MIT license

**Why it matters for VIP:**
- Free, open-source, no per-page pricing
- Superior table and layout understanding vs. naive PDF parsing
- Direct integration with Spring AI via `TikaDocumentReader` (Apache Tika underneath) or custom `DocumentReader`
- IBM Granite-Docling-258M: new ultra-compact VLM for document-to-structured-format conversion
- Ideal for floor plan PDFs, spec sheets with tables, multi-column venue decks

**Strategic decision for VIP:** Use Docling as the primary document parsing layer. It handles the structural extraction (layout, tables, text) and Spring AI's ETL pipeline then handles chunking, embedding, and vector storage.

---

#### Apache Tika
**What it is:** The Java ecosystem's gold standard for file format detection and text extraction. Detects and parses 1000+ file types through a single interface.

**What it does:**
- Text extraction from: PDF, DOC/DOCX, XLS/XLSX, PPT, images (via OCR), HTML, XML, ZIP, and 1000+ more
- Metadata extraction (author, creation date, dimensions)
- Language detection
- MIME type detection
- Tika Pipes: fault-tolerant, scalable processing (each file in a forked JVM with timeout/memory limits)

**Integration with Spring AI:**
- Spring AI ships `TikaDocumentReader` out of the box
- `TikaDocumentReader` wraps Tika behind Spring AI's `DocumentReader` interface
- Handles: PDFs, Word, Excel, PowerPoint transparently — same API regardless of file type
- Used in production for: search engine indexing, content analysis, translation pipelines

**Why it's the right choice for VIP:**
- Battle-tested in enterprise Java for 15+ years
- DWG/DXF support via Tika's AutoCAD parser (direct path for CAD files)
- Zero extra infrastructure — runs in-process
- Tika Pipes for production safety (malformed files can't crash the service)
- Already in Spring AI's ETL pipeline — no custom integration needed

---

#### LlamaIndex / LangChain
**What they are:** Python-first AI orchestration frameworks. LlamaIndex has strong document parsing (LlamaExtract, LlamaParse). LangChain has document loaders.

**Relevance to VIP:** These are Python-ecosystem tools. Since VIP is Java/Spring Boot, they are not directly applicable. Spring AI is the Java equivalent and has caught up rapidly.

**Note:** If VIP ever needs a Python microservice for specialized extraction (e.g., advanced floor plan analysis), LlamaIndex's LlamaParse is best-in-class for complex PDFs.

---

#### Reducto, Raydocs, Retab (Document Intelligence Startups)
Recent well-funded entrants in the document intelligence space:
- **Reducto** — $75M Series B (a16z, Benchmark, YC). API-first structured extraction from complex documents.
- **Raydocs** — Template-based extraction with confidence scores and source links.
- **Retab** — Pre-seed $3.5M. Non-technical users building extraction templates.

**Pattern:** All these companies are *horizontal* document intelligence APIs. VIP's opportunity is to be *vertical* — deeply specialized for venue documents (floor plans, venue decks, CAD files, spec sheets). Horizontal tools extract generic fields. VIP extracts venue-specific intelligence with a purpose-built schema.

---

### 1.3 Competitive Gap Summary

| Capability | Cvent | Tripleseat | Momentus | Unstructured.io | VIP |
|---|---|---|---|---|---|
| Venue discovery (marketplace) | ✅ Best-in-class | ⛔ | ⛔ | ⛔ | Phase 3 |
| Planner's own venue library | ⛔ | ⛔ | ⛔ | ⛔ | ✅ |
| Document intelligence (PDF, floor plans) | ⛔ | ⛔ | ⛔ | ✅ (infra only) | ✅ |
| AI metadata extraction | ⛔ | ⛔ | ⛔ | ✅ (generic) | ✅ (venue-specific) |
| CAD file support (DWG/DXF) | ⛔ | ⛔ | ⛔ | ⛔ | ✅ (via Tika) |
| Semantic search (vector) | ⛔ | ⛔ | ⛔ | ⛔ | ✅ |
| Team collaboration / shared library | Partial | ⛔ | ✅ | ⛔ | ✅ |
| Multi-tenant SaaS | ✅ | ✅ | ✅ | ✅ | ✅ |
| Venue-specific schema | ⛔ | ✅ (operations) | ✅ (operations) | ⛔ | ✅ (intelligence) |
| SMB-friendly pricing | ⛔ | Partial | ⛔ | ✅ | ✅ |

**The gap VIP fills:** Nobody provides document intelligence specifically for event planners managing their own venue portfolio. Existing tools either focus on venue operations (Tripleseat, Momentus) or are generic document APIs (Unstructured.io). VIP is the venue-specific intelligence layer on top of proven document parsing infrastructure.

---

## 2. ETL Pipeline Architecture — Proven Foundation

### 2.1 Spring AI's ETL Pipeline

Spring AI ships a first-class, production-grade ETL pipeline with three composable stages:

```
DocumentReader  →  DocumentTransformer  →  DocumentWriter
   (Extract)           (Transform)            (Load)
```

**DocumentReaders (Extract) — available out of the box:**

| Reader | Handles | Notes |
|---|---|---|
| `TikaDocumentReader` | PDF, DOCX, XLSX, PPTX, HTML, XML, 1000+ formats | Apache Tika under the hood. **Primary reader for VIP.** |
| `PagePdfDocumentReader` | PDFs, page-by-page | Preserves page boundaries, useful for floor plans |
| `ParagraphPdfDocumentReader` | PDFs, paragraph-level | Better semantic chunking for venue decks |
| `MarkdownDocumentReader` | Markdown files | Useful for structured venue specs |
| `JsonMetadataReader` | JSON with metadata | Useful for structured imports |
| `JsoupDocumentReader` | HTML pages | Web scraping venue information |

**DocumentTransformers (Transform):**

| Transformer | What it does |
|---|---|
| `TokenTextSplitter` | Splits large documents into chunks respecting token limits |
| `ContentFormatTransformer` | Normalizes text format |
| `SummaryMetadataEnricher` | Generates document summary using LLM, stored as metadata |
| `KeywordMetadataEnricher` | Extracts keywords using LLM, stored as metadata |
| Custom `VenueMetadataEnricher` | **VIP-specific:** extracts capacity, amenities, contacts via structured LLM call |

**DocumentWriters (Load):**

| Writer | What it does |
|---|---|
| `PgVectorStore` | Writes chunks + embeddings to PostgreSQL pgvector |
| `SimpleVectorStore` | In-memory (testing/dev) |
| `FileDocumentWriter` | Write to files (useful for debugging pipeline) |

### 2.2 VIP's Document Processing Pipeline

```
                     S3 Asset Storage
                          │
                          │ presigned URL download
                          ▼
               ┌─────────────────────┐
               │  DocumentReader     │  Spring AI / Apache Tika
               │  (per asset type)   │  + IBM Docling (PDF tables)
               └──────────┬──────────┘
                          │  List<Document>
                          │  (raw text chunks + page metadata)
                          ▼
               ┌─────────────────────┐
               │  DocumentSplitter   │  TokenTextSplitter
               │                     │  (512 tokens, 50 overlap)
               └──────────┬──────────┘
                          │  List<Document> (chunks)
                          ▼
               ┌─────────────────────┐
               │  VenueMetadata      │  GPT-4o structured output
               │  Enricher           │  → capacity, amenities, contacts
               └──────────┬──────────┘
                          │  List<Document> + venue metadata
                          ▼
               ┌─────────────────────┐
               │  EmbeddingModel     │  text-embedding-3-small
               │                     │  (1536 dimensions per chunk)
               └──────────┬──────────┘
                          │  List<Document> + float[] embeddings
                          ▼
               ┌─────────────────────┐
               │  TenantAware        │  PostgreSQL + pgvector
               │  PgVectorStore      │  per-tenant schema
               └──────────┬──────────┘
                          │
                          ▼
               ┌─────────────────────┐
               │  MetadataAggregator │  Event-sourced consolidation
               │                     │  (conflict resolution)
               └─────────────────────┘
```

**Java implementation sketch:**
```java
@Service
@RequiredArgsConstructor
public class VenueAssetProcessingPipeline {

  private final TikaDocumentReader.Factory tikaFactory;
  private final TokenTextSplitter splitter;
  private final VenueMetadataEnricher enricher;   // custom Spring AI DocumentTransformer
  private final EmbeddingModel embeddingModel;
  private final VectorStore vectorStore;
  private final MetadataAggregationService aggregationService;

  public void process(VenueAsset asset, byte[] content) {
    // 1. Extract — Tika handles PDF, DOCX, XLSX, images via OCR, DWG
    var reader = tikaFactory.create(new ByteArrayResource(content), asset.getContentType());
    var rawDocs = reader.get();

    // 2. Enrich raw docs with source metadata
    var taggedDocs = rawDocs.stream()
      .map(doc -> doc.mutate()
        .metadata("venue_id", asset.getVenueId())
        .metadata("asset_id", asset.getId())
        .metadata("asset_type", asset.getType())
        .metadata("tenant_id", TenantContext.getCurrentTenantId())
        .build())
      .toList();

    // 3. Split into semantic chunks
    var chunks = splitter.apply(taggedDocs);

    // 4. Enrich with venue-specific metadata (LLM structured call)
    var enrichedChunks = enricher.apply(chunks);

    // 5. Embed + write to pgvector
    vectorStore.add(enrichedChunks);

    // 6. Aggregate extracted metadata into venue profile
    var extractedMetadata = enricher.getLastExtractionResult();
    aggregationService.applyExtractionEvent(asset, extractedMetadata);
  }
}
```

### 2.3 Asset-Type Processing Matrix

| Asset Type | Parser | OCR Needed | Structured Extraction | Vector Indexed |
|---|---|---|---|---|
| PDF Deck (text) | Tika → ParagraphPdfDocumentReader | No | Yes (GPT-4o) | Yes |
| PDF Deck (scanned) | Tika + OCR (Tesseract) | Yes | Yes (GPT-4o) | Yes |
| Floor Plan (PDF) | Docling (layout-aware) | No | Yes (GPT-4o vision) | Yes |
| Floor Plan (image) | GPT-4o vision direct | Yes (LLM) | Yes | Yes |
| Photos | GPT-4o vision | No | Amenity detection | Yes |
| DOCX / Technical Spec | TikaDocumentReader | No | Yes (GPT-4o) | Yes |
| XLSX (capacity tables) | TikaDocumentReader | No | Structured parsing | Yes |
| DWG / DXF (CAD) | Tika AutoCAD parser | No | Metadata only | Metadata only |
| Video (walkthrough) | Extract thumbnail + audio | Via Whisper | Partial | Partial (Phase 2) |

### 2.4 Chunking Strategy

Document chunking significantly impacts retrieval quality. VIP uses a hybrid strategy:

**For venue decks (PDFs):**
- `ParagraphPdfDocumentReader`: preserves paragraph structure
- Chunk size: 512 tokens with 50-token overlap
- Metadata per chunk: page number, section heading, asset ID, confidence tier

**For floor plans (images/PDFs with diagrams):**
- Page-level chunking (one Document per page)
- Attach full-page image for GPT-4o vision processing
- Extract: room names, dimensions, capacity annotations

**For spec sheets (tables):**
- Docling's `TableFormer` model reconstructs table cells
- Each table row becomes a document with column headers as metadata
- Preserves relational structure: `{"room": "Grand Ballroom", "capacity_banquet": 400, "capacity_theater": 600}`

**For photos:**
- No text chunking — pass directly to GPT-4o vision
- Single Document per image with vision-extracted metadata

### 2.5 Why Apache Tika is the Right Foundation

Apache Tika has been the Java ecosystem's battle-tested document parser since 2007:

- **1000+ file formats** — one interface regardless of file type
- **Tika Pipes** — each file processed in forked JVM; a malformed or malicious file cannot crash the service
- **Production-proven** — used by Elasticsearch, Solr, Apache Nutch, enterprise search systems globally
- **DWG/DXF support** — AutoCAD format parser built in (rare capability — most alternatives don't support this)
- **Direct Spring AI integration** — `TikaDocumentReader` ships with Spring AI, no custom code needed
- **Zero cloud dependency** — runs in-process, no API call needed for text extraction

**Tika Pipes configuration for production safety:**
```java
@Bean
public TikaDocumentReader.Factory tikaReaderFactory() {
  return TikaDocumentReader.Factory.builder()
    .withTikaConfig(TikaConfig.getDefaultConfig())
    .withForkParser(true)          // Each file in isolated JVM
    .withParseTimeout(60)          // 60s max per file
    .withMaxMemory(256 * 1024 * 1024)  // 256MB per file
    .build();
}
```

### 2.6 Docling Integration for High-Fidelity PDF Parsing

For venue decks with complex layouts, tables, and multi-column structures, Docling outperforms basic Tika PDF parsing:

**When to use Docling over Tika for PDFs:**
- Multi-column layouts (most venue decks)
- Capacity tables (banquet vs. theater vs. classroom setups)
- Mixed text + diagram pages (floor plans embedded in PDFs)
- Scanned PDFs requiring layout-aware OCR

**Integration approach:**
```java
// Spring AI custom DocumentReader wrapping Docling HTTP API
@Component
public class DoclingDocumentReader implements DocumentReader {

  private final DoclingClient doclingClient; // REST client to Docling service

  @Override
  public List<Document> get() {
    var doclingResult = doclingClient.convert(resource, ConversionOptions.builder()
      .withTableExtraction(true)
      .withOcr(resource.getContentType().contains("image"))
      .build());

    return doclingResult.getChunks().stream()
      .map(chunk -> Document.builder()
        .content(chunk.getText())
        .metadata("page", chunk.getPage())
        .metadata("element_type", chunk.getType()) // TEXT, TABLE, FIGURE
        .metadata("table_data", chunk.getTableJson())
        .build())
      .toList();
  }
}
```

**Docling deployment (self-hosted, zero cost):**
```yaml
# docker-compose.yaml addition
docling-service:
  image: ds4sd/docling-serve:latest
  ports:
    - "5001:5001"
  environment:
    - DOCLING_WORKER_THREADS=4
```

**IBM Granite-Docling-258M** (released 2026): An ultra-compact VLM that converts documents to structured formats while preserving layout, tables, equations. Can replace Docling's heavy ML models with a lighter inference endpoint for cost-sensitive deployments.

---

## 3. The Intelligence Layer VIP Owns

Everything above (Tika, Docling, Spring AI ETL) is infrastructure. VIP's proprietary intelligence sits on top:

### 3.1 Venue-Specific Extraction Schema

Generic document intelligence tools extract generic fields. VIP extracts fields that matter for event professionals:

```json
{
  "venue_profile": {
    "capacity": {
      "max_total": 500,
      "configurations": {
        "banquet": 300,
        "theater": 500,
        "classroom": 200,
        "cocktail": 450,
        "conference": 150
      }
    },
    "venue_type": ["conference_center", "hotel_ballroom"],
    "location": {
      "address": "...",
      "neighborhood": "Midtown",
      "proximity_notes": "3 blocks from Grand Central"
    },
    "catering": {
      "policy": "in_house_exclusive",
      "kosher_available": true,
      "halal_available": false,
      "outside_catering_allowed": false
    },
    "av_tech": {
      "built_in_av": true,
      "projector_lumens": 5000,
      "screens": 2,
      "rigging_points": true,
      "internet_bandwidth_mbps": 1000
    },
    "accessibility": {
      "ada_compliant": true,
      "elevator_access": true,
      "accessible_restrooms": true,
      "wheelchair_stage_access": false
    },
    "logistics": {
      "load_in_access": "freight_elevator",
      "parking_spaces": 200,
      "valet_available": true,
      "curfew_time": "23:00"
    },
    "restrictions": ["no_open_flame", "no_confetti", "no_outside_alcohol"],
    "contacts": [
      {"name": "...", "role": "venue_sales", "email": "...", "phone": "..."}
    ],
    "pricing": {
      "minimum_spend": 10000,
      "currency": "USD",
      "rental_fee_indicative": 5000
    }
  }
}
```

This schema is what makes VIP a *venue intelligence platform*, not just a document storage system. Every competitor either has operational data (bookings, invoicing) or generic extraction. No one has this schema purpose-built for event planners.

### 3.2 Confidence-Sourced Metadata Model

Each field carries full provenance:

```json
"capacity.max_total": {
  "value": 500,
  "confidence": 0.94,
  "source_type": "PDF_DECK",
  "source_page": 4,
  "extraction_model": "gpt-4o-2024-08-06",
  "alternatives": [
    { "value": 480, "confidence": 0.72, "source_type": "FLOOR_PLAN" }
  ],
  "overridden_by": null
}
```

No existing venue tool surfaces this level of data provenance. Users see not just the value but *why* the system believes it.

### 3.3 Multi-Source Aggregation (The Hard Problem Nobody Solves)

Venues send the same venue in multiple formats — a marketing deck, a floor plan PDF, a technical spec sheet, a photo set. Each source may have conflicting or complementary data.

VIP's aggregation engine:
1. Collects all extraction events per venue (event log)
2. Applies priority rules: `manual_override > verified > high_confidence_AI > low_confidence_AI`
3. For arrays (amenities, restrictions): set-union with confidence weighting
4. Surfaces conflicts in the UI: "AI found two different capacity values — which is correct?"
5. Allows one-click resolution

This is a genuine product moat. No other platform in the event space does this.

---

## 4. Scalability Architecture

### Event-Driven, Horizontally Scalable

```
Upload → S3 → AssetUploadedEvent → RabbitMQ → N consumers → Processing Pipeline
                                                    ↑
                                              Scale consumers
                                              based on queue depth
```

- Each extraction job is independent — N workers can run in parallel
- Priority queues by plan tier (Enterprise processes first)
- Dead-letter queue catches failures — automatic retry with backoff
- No single point of failure in the processing path

### Cost Scaling

| Scale | AI Cost | Infrastructure |
|---|---|---|
| 100 venues (MVP) | ~$0.10 | Single container |
| 10K venues | ~$10 | 2-3 containers |
| 1M venues | ~$1,000 | Auto-scaled, still manageable |
| 100M venues | ~$100K | Optimize with cheaper models + caching |

At the $0.001/venue cost of GPT-4o extraction + embedding generation, VIP can process 1 million venues for approximately $1,000 in AI costs. This is not a cost problem.

### Vector Search Scaling

pgvector with IVFFlat index:
- Sub-10ms semantic search at 1M venues
- Scales to ~5M vectors per instance before needing optimization (HNSW index or Pgvector cloud)
- Multi-tenant isolation via schema-per-tenant — no cross-tenant data leakage

---

## 5. Technology Decisions Summary

| Layer | Choice | Rationale |
|---|---|---|
| **Document parsing** | Apache Tika (via Spring AI `TikaDocumentReader`) | 1000+ formats, DWG support, fault-tolerant Pipes, built into Spring AI |
| **PDF layout analysis** | IBM Docling (self-hosted, open source) | State-of-the-art table/layout extraction, MIT license, no per-page cost |
| **AI framework** | Spring AI 1.0 | Java-native, provider-agnostic, ETL pipeline built-in, Micrometer metrics |
| **LLM extraction** | OpenAI GPT-4o | Best structured output, multimodal (vision for images/floor plans) |
| **Embeddings** | OpenAI text-embedding-3-small | 1536 dims, $0.02/1M tokens, excellent quality/cost ratio |
| **Vector store** | pgvector (PostgreSQL extension) | No extra service, transactional, tenant-isolated, production-ready |
| **Full-text search** | PostgreSQL tsvector | Unified with relational data, no extra service |
| **Geo search** | PostGIS (PostgreSQL extension) | Mature, no extra service |
| **Async processing** | RabbitMQ (existing foundation) | Already in platform, priority queues, DLQ |
| **File storage** | S3 / MinIO (existing foundation) | Already in IAM service, same pattern |

**Principle:** Use proven infrastructure that already exists in the IQKV foundation. Introduce the minimum number of new services. The only truly new infrastructure is pgvector (a PostgreSQL extension, not a new service) and optionally a self-hosted Docling container for advanced PDF parsing.

---

## 6. Open Questions for Implementation

- **Docling vs. pure Tika:** Start with Tika for MVP speed. Add Docling for Phase 2 when floor plan fidelity matters.
- **OCR strategy:** Tika bundles Tesseract for basic OCR. GPT-4o vision handles complex cases. Threshold: if Tika OCR confidence < 0.7, escalate to GPT-4o vision.
- **CAD files:** Tika extracts metadata from DWG/DXF (dimensions, layers). For Phase 1, expose raw metadata. Phase 2: convert to PNG via LibreCAD/ODA, then GPT-4o vision for layout understanding.
- **Video walkthroughs:** Out of scope for Phase 1. Phase 2: extract keyframes (ffmpeg), run GPT-4o vision on representative frames.
- **Chunking overlap:** 50-token overlap is standard. Venue-specific content (e.g., capacity tables) should use smaller chunks (256 tokens) to preserve row-level precision.
- **Embedding freshness:** Re-embed when manual overrides change the consolidated metadata. Trigger via `MetadataAggregatedEvent`. Don't re-embed unchanged chunks.

---

**Document type:** Technical intelligence reference
**Stage:** Pre-build design
**Audience:** Engineering, founding team
