# GenAI Assignment:

**Evaluation Criteria**

We will score your submission on:

* Clarity and practicality of architecture
* Robust JSON schema design
* Prompt quality (zero-shot, reliable, minimal hallucination risk)
* Handling of ambiguity + user review flow
* Bulk generation thinking (errors, naming, report)

## Problem 1: **Proposal for “Video-to-Notes”**

We have a local folder of long videos (3–4 hours each, 200MB+). Watching them fully is slow. We need an automated way to generate a “summary package” per video: **Summary.md** + highlight clips + screenshots, all organized per video. [READ MORE ABOUT THE PROJECT](./Video-summary-platform.md)

### **Task**

Prepare a **pre-processed solution proposal** comparing  **three approaches** **:**

1. **Online/Cloud-Based (Already Available Solutions)**
2. **Build Our Own Using LLM APIs (Hybrid: local media processing + cloud LLM)**
3. **Build Fully Offline Using Open-Source Models (Local transcription + local LLM + pipeline)**

No code required. We want a **clear, practical proposal** with architecture and tradeoffs.

### Your Solution for problem 1:

## Problem Recap

What we need is a batch, scalable system that converts long, large videos into a 5–10 minute consumable summary package with timestamps, clips, screenshots, and a structured Markdown note.

## Common Pipeline for all approaches
It doest matter which approach we use but the logical pipeline for all the approaches will me the same

1. Video ingestion
2. Audio extraction
3. Speech-to-text transcription with timestamps
4. Semantic analysis 
5. Timestamp selection
6. Media extraction
7. Short clips
8. Key screenshots
9. Structured Markdown generation
10. Predictable folder output

The difference only is that where we want to do our transcription and LLM part

# Approach 1: Online or Cloud-Based :

Description: In this solution we use fully managed cloud platforms that already provide video understanding, transcription, and summarization.

## Typical Stack : 

  1. Upload videos to cloud
  2. Cloud AI handles transcription, summarization, highlights
  3. Download summaries and assets

## Example Tech Providers :

  1. Google Cloud - Video Intelligence API
  2. AWS - (Transcribe + Bedrock)
  3. Microsoft Azure - Video Indexer

## Architecture :

     Local Folder
      ↓ Upload   
    Cloud Video AI Platform
     → Transcription
     → Highlights
     → Summaries
     → Asset extraction
     ↓ Download
    Local Output Folder

## Tradeoffs:

  pros : 
  1. Very minimal development effort
  2. High accuracy from very refined commerical models
  3. Fast Setup in days or weeks
  4. Scalable for large volumes via cloud resources

  cons : 
  1. Ongoing cost (e.g., $0.10–$0.50 per minute of video)
  2. Privacy risks
  3. Potential rate limits or downtime
  4. Internet dependency
  5. Less customization
 
## Best Fit :

  1. Proof-of-concept
  2. Low volume, high budget
  3. Non-sensitive content


# Approach 2: Build our own Using LLM APIs (Hybrid : Local Media + Cloud LLM APIs):

Description : Process video/audio locally, but use cloud LLM APIs for intelligence (summarization, highlight extraction).

## Core Idea

1. Heavy files stay local.
2. Only text (transcripts) go to the cloud.

## Typical Stack

1. Local: FFmpeg, Python pipeline
2. Cloud: LLM APIs for reasoning and summarization

## Example Models / APIs

1. OpenAI (GPT-4 / GPT-4.1)
2. Anthropic (Claude)
3. Groq (fast inference)

## Architecture

		         Local Videos
		             ↓
		     Audio Extraction (Local)
		             ↓
		    Speech-to-Text (Local or Cloud)
		             ↓
		     Transcript + Timestamps
		             ↓
	Cloud LLM (Summaries, Highlights, Actions)
		             ↓
		      Timestamp Mapping
		             ↓
		Clip & Screenshot Extraction (Local)
		             ↓
		     Markdown + Assets Output


## Tradeoffs

Pros

1. Excellent balance of cost vs quality
2. No large video uploads
3. Full control over summary format
4. Easy to iterate and improve
5. Industry-preferred architecture

Cons

1. Requires pipeline design
2. API costs at scale
3. Internet dependency

## Best Fit

1. Intern-friendly production system
2. Startups & internal tooling
3. High flexibility, moderate budget

# Approach 3: Fully Offline (Open-Source, Local-Only)

Description : Entire pipeline runs offline, including transcription and summarization.

## Typical Stack

1. Media: FFmpeg
2. Transcription: Whisper
3. LLMs: LLaMA / Mistral 
4. Vector search : FAISS

## Architecture

								Local Videos
								   ↓
							Audio Extraction
								   ↓
					  Local Transcription (Whisper)
								   ↓
						 Chunked Transcript
								   ↓
					Local LLM (Summaries, Highlights)
								   ↓
						  Timestamp Selection
								   ↓
					Clip & Screenshot Extraction
								   ↓
						Markdown + Assets Output

## Tradeoffs

Pros

1. No API cost
2. Full data privacy
3. Works without internet
4. No vendor lock-in

Cons

1. Requires powerful hardware (GPU)
2. Slower processing
3. Lower summary quality vs cloud LLMs
4. Higher engineering complexity

## Best Fit

1. Privacy-critical environments
2. On-prem deployments
3. Research / cost-sensitive long-term use


# Recommended approach (from the above three )

As of now we have analyzed all the three approaches of our Video --> Summary platform . We have understand the basic logical working and structure of all three approaches and the tradeoffs of using one approach over other . So now we have to decide that what approach we use on the basis of our constraints such as : 

1. Cost
2. Privacy
3. scalability
4. speed
5. Accuracy
6. Set up effort

## Hybrid Architecture (Local Media Processing + Cloud LLM)

This solution processes large media locally and uses cloud LLM APIs only for reasoning on text, which offers the best balance between Privacy ,quality, cost, and scalability.

## High-Level Architecture
					
				Input Folder (Videos)
					   ↓
		Preflight Scan (metadata, duration)
					   ↓
			Audio Extraction (local)
					   ↓
			Timestamped Transcription
					   ↓
				Transcript Chunking
					   ↓
			LLM Reasoning (highlights, summary)
					   ↓
		Validation & Ambiguity Detection
					   ↓
	Clip & Screenshot Extraction (local)
					   ↓
		Structured Output (JSON + Markdown)


## Data & Output Design (JSON-First)

Although the final deliverable is Summary.md, the source of truth is a structured JSON file. Markdown is rendered from JSON.

Core Output Schema :


										{
									  "video_id": "string",
									  "filename": "string",
									  "duration_seconds": 0,
									  "processed_at": "ISO-8601",
									
									  "summary": {
									    "high_level": "string",
									    "confidence": 0.0
									  },
									
									  "highlights": [
									    {
									      "id": "hl_01",
									      "start_time": "HH:MM:SS",
									      "end_time": "HH:MM:SS",
									      "title": "string",
									      "description": "string",
									      "importance": "low|medium|high",
									      "ambiguity_flag": false
									    }
									  ],
									
									  "takeaways": [
									    {
									      "type": "action|insight|decision",
									      "text": "string"
									    }
									  ],
									
									  "assets": {
									    "clips": [],
									    "screenshots": []
									  },
									
									  "review": {
									    "needs_human_review": false,
									    "notes": []
									  }
									}

## Prompting Strategy (Zero-Shot, Reliable)

1. Role + clear task.
2. Chain-of-Thought encouragement.
3. Strict output format (JSON schema pasted in prompt).
4. Anti-hallucination instructions: "Base ONLY on transcript. Do NOT invent facts, speakers, or timestamps. If uncertain, set confidence low and add to  low_confidence_flags."
5. Timestamp accuracy: "Timestamps MUST be integers or floats matching transcript seconds. Prefer exact segment starts/ends from transcript cues."
6. Length control: "High-level summary: 100-200 words. 5-10 highlights max."

## Example snippet:

		
			You are an expert video content analyst. Your task is to create a concise, accurate summary package from the provided timestamped transcript.
			
			Rules:
			- Stick strictly to the transcript content. Do NOT add external knowledge, hallucinate events, speakers, or timings.
			- Timestamps must be precise integers/floats in seconds from the transcript.
			- Output ONLY valid JSON matching this exact schema: [paste full schema here]
			- If content is ambiguous/noisy, note in low_confidence_flags and lower confidence.
			- Highlights: select 5-10 most important, non-redundant moments; include actionable key_takeaway for each.
			
			Transcript:
			[TRANSCRIPT_HERE]

This minimizes hallucination via grounding + schema enforcement + confidence mechanism

## Handling Ambiguity & Human Review

Ambiguity is treated as a first-class output, not an error.

### When ambiguity is flagged

Overlapping speakers
Poor audio quality
Vague or context-dependent discussions

Review Flow

							LLM Output
							   ↓
					Ambiguity Detection
							   ↓
				review.needs_human_review = true
							   ↓
			Human validates or edits highlights


In Summary.md, ambiguous highlights are clearly marked, ensuring transparency.

## Batch Processing & Robustness

Folder Structure
				
				output/
				  video_name/
				    Summary.md
				    summary.json
				    clips/
				    screenshots/
				    logs/

				
															
## Bulk Error Handling

One failed video does not stop the batch
Transcription or LLM failures are retried
Partial outputs are preserved
All errors are logged

## Batch Report 
				{
				  "total_videos": 20,
				  "processed": 18,
				  "failed": 2,
				  "needs_review": 5
				}


This enables monitoring and operational confidence.

## Conclusion

This hybrid solution is production-ready in design while remaining simple enough for incremental implementation. It scales to large videos, minimizes hallucination risk, supports human review, and produces consistent, high-quality summary packages.

It balances engineering rigor with practical constraints, making it suitable for real-world deployment.

## Problem 2: **Zero-Shot Prompt to generate 3 LinkedIn Post**

Design a **single zero-shot prompt** that takes a user’s persona configuration + a topic and generates **3 LinkedIn post drafts** in **3 distinct styles**, each aligned to the user’s voice and constraints. The output must be structured so the app can: show 3 drafts to the user. Assume we are consuming **OpenAI API / Gemini API** with **one prompt call** (no fine-tuning). Your prompt must reliably produce valid, structured output. [READ MORE ABOUT THE PROJECT](./linkedin-automation.md)

**TASK:** Write a prompt that can work.

### Your Solution for problem 2:

## Problem recap :
Posting consistently on LinkedIn is difficult and time-consuming, and generic automation tools often produce low-quality or inauthentic content. The goal is to build a system that preserves a user’s persona, generates three distinct post styles per topic, requires explicit approval, and securely publishes posts immediately or on a scheduled timeline.

Our task it so to create a prompt that works with the given constarints and perform well
                                              
# Final Prompt: LinkedIn Post Generation

## System Prompt

You are an expert LinkedIn content writer and editor.

Your task is to generate LinkedIn-ready posts that strictly follow the user’s persona, tone, language style, and content rules.

## Hard Rules -->> Must follow 

1. You must preserve the user’s voice and constraints at all times.
2. Do not add emojis, hashtags, CTAs, formatting styles, or phrases unless explicitly allowed.
3. Do not invent facts about the user’s background.
4. Avoid spammy, repetitive, clickbait, or policy-violating content.
5. Each post must be ready to publish as-is.
6. Each post must be meaningfully different in structure and style, while staying consistent with the same persona.
7. If the topic or context is ambiguous, ask for clarification instead of guessing.
8. You must output only structured JSON matching the schema provided.

## User Prompt

### Persona Configuration
			{
			  "background": "<user background / experience>",
			  "tone": "<tone preferences, e.g. calm, confident, reflective>",
			  "language_style": "<formal / conversational / concise / storytelling>",
			  "dos": ["<allowed practices>"],
			  "donts": ["<forbidden phrases, styles, or behaviors>"]
			}

## Topic & Context
			
			{
			  "topic": "<main topic>",
			  "optional_context": "<optional background or angle>",
			  "target_audience": "<who this post is for>",
			  "goal": "<inform / share insight / build credibility / spark discussion>"
			}

## Output Requirements

A) Generate exactly 3 LinkedIn posts
B) Each post must be 600–1,200 characters
C) Each post must use a distinct style:
  1. Concise Insight
  2. Story-Based Reflection
  3. Actionable Checklist or Framework
D) All posts must:
  1. Match the same persona
  2. Communicate the same core idea
  3. Differ clearly in structure and delivery

Do not include posting time, scheduling, or platform actions

## Required Output Schema (JSON Only)
			{
			  "posts": [
			    {
			      "id": "post_1",
			      "style": "concise_insight",
			      "content": "string",
			      "self_check": {
			        "persona_match": true,
			        "style_distinct": true,
			        "spam_risk": "low|medium",
			        "policy_risk": "low",
			        "confidence": 0.0
			      }
			    },
			    {
			      "id": "post_2",
			      "style": "story_based",
			      "content": "string",
			      "self_check": {
			        "persona_match": true,
			        "style_distinct": true,
			        "spam_risk": "low|medium",
			        "policy_risk": "low",
			        "confidence": 0.0
			      }
			    },
			    {
			      "id": "post_3",
			      "style": "actionable_checklist",
			      "content": "string",
			      "self_check": {
			        "persona_match": true,
			        "style_distinct": true,
			        "spam_risk": "low|medium",
			        "policy_risk": "low",
			        "confidence": 0.0
			      }
			    }
			  ],
			  "generation_notes": {
			    "ambiguities_detected": false,
			    "needs_regeneration": false,
			    "notes": ""
			  }
			}


## Mandatory Style Separation

1. Concise Insight

Direct, analytical
1–2 short paragraphs
Focus on a clear takeaway

2. Story-Based Reflection

Narrative or reflective tone
Uses experience or observation (without inventing facts)
Slower pacing, emotional clarity

3. Actionable Checklist / Framework

Structured steps or bullets
Practical and instructional
No motivational fluff

If meaningful separation is not possible, flag it.

## Failure & Regeneration Rules

1. If persona rules conflict with topic → set needs_regeneration = true
2. If style separation is weak → set needs_regeneration = true
3. If ambiguity exists → do not guess; document it
4. Never silently degrade quality



## Problem 3: **Smart DOCX Template → Bulk DOCX/PDF Generator (Proposal + Prompt)**

Users have many Word documents that act like templates (offer letters, certificates, invoices, contracts). They repeatedly change only a few fields (name, date, amount, address, role, etc.). Doing this manually is slow and error-prone, especially in bulk. [READ MORE ABOUT THE PROJECT](./docs-template-output-generation.md)

We want a system that:

1. Converts an uploaded **DOCX** into a reusable **template** by identifying editable fields.
2. Supports **single generation** (form-fill → DOCX/PDF download).
3. Supports **bulk generation** via **Excel/Google Sheet** rows.

### **Task (No coding)**

Submit a **proposal** for building this system using GenAI (OpenAI/Gemini) for “template field detection” and “field schema generation”. We want a practical design, not code.

### Your Solution for problem 3:

# Objective

Build a system that converts a standard DOCX document into a reusable structured template, enabling:

1. Single document generation 
2. Bulk generation (Excel / Google Sheets)
3. Output as DOCX and/or PDF
4. Reliable reporting and error handling

### GenAI will be used for:
1. Detecting editable fields
2. Suggesting structured field schemas
3. Identifying optional blocks
4. Improving usability while preserving formatting

## High-Level Architecture

							User Uploads DOCX
							        ↓
				 DOCX Parser (structure extraction)
							        ↓
				Text + Structure → GenAI Field Detection
							        ↓
					AI Suggested Field Schema (JSON)
							        ↓
						User Review & Confirmation
							        ↓
							Template Saved

							
							Generation Phase:
							Data Input (Form / Sheet)
							        ↓
							Validation Engine
							        ↓
							Template Renderer (DOCX)
							        ↓
							PDF Conversion 
							        ↓
							   ZIP + Report


## Role of GenAI in the System

GenAI is used only for intelligence tasks, not rendering.

3.1 Template Field Detection
When a DOCX is uploaded:

1. Extract:

Text content
Tables
Repeated placeholders
Formatting blocks

2. Send structured text to GenAI

## Example Extract Sent to AI

			Dear Mr. Lakshya Gautam,
			
			We are pleased to offer you the position of Gen Ai Intern at DivamTech.
			Your salary will be ₹8,00,000 per annum.
			Start date: 1 March 2026.
			
			Sincerely,
			HR Team

## AI Task

Detect variable fields
Suggest field names
Infer types
Identify optional sections
Output strict JSON schema

## 3.2 Expected AI Output Schema
			{
			  "fields": [
			    {
			      "field_id": "candidate_name",
			      "label": "Candidate Name",
			      "type": "text",
			      "required": true,
			      "example_value": "John Doe",
			      "confidence": 0.95
			    },
			    {
			      "field_id": "salary",
			      "label": "Annual Salary",
			      "type": "currency",
			      "required": true,
			      "currency": "INR"
			    },
			    {
			      "field_id": "start_date",
			      "label": "Start Date",
			      "type": "date",
			      "required": true,
			      "format": "DD MMMM YYYY"
			    }
			  ],
			  "optional_blocks": [
			    {
			      "block_id": "probation_clause",
			      "description": "Probation clause paragraph",
			      "detection_hint": "Contains word 'probation'"
			    }
			  ]
			}

## Template Creation Workflow

### Step 1: Upload DOCX

System parses document structure.

### Step 2: AI Suggestion

GenAI proposes:

Editable fields
Field types
Optional conditional blocks

### Step 3: User Confirmation

User can:
Rename fields
Change types
Mark required/optional
Add missing fields
Delete incorrect suggestions

### Step 4: Template Save

Template stored with:
Raw DOCX
Field schema (JSON)
Metadata

## Document Rendering Strategy

Important: AI is not used during rendering.

Rendering uses deterministic logic:
Replace placeholders with validated values
Preserve formatting
Maintain:
		Tables
		Headers/footers
		Logos
		Signatures
		Page breaks

PDF generated from final DOCX.

***“GenAI is intentionally limited to template field detection and schema suggestion during template creation; all document rendering and bulk generation remain deterministic to ensure reliability and formatting integrity.”***


## Single Document Generation

Workflow:

							Select Template
							      ↓
				Auto-generated Form (from schema)
							      ↓
							Field Validation
							      ↓
							   Render DOCX
							      ↓
					Convert to PDF (optional)
							      ↓
							   Download

## Bulk Generation Design

### 7.1 Sheet Format Auto-Generation

System generates downloadable Excel:

candidate_name	salary	start_date

Columns strictly match schema field IDs.

### 7.2 Bulk Execution Flow

				Upload Sheet / Connect Google Sheet
				         ↓
				Row-by-row validation
				         ↓
				Render documents independently
				         ↓
				Generate ZIP bundle
				         ↓
				Produce generation report

### 7.3 Error Handling (Row-Level)

Example report:
			
			{
			  "total_rows": 500,
			  "successful": 472,
			  "failed": 28,
			  "errors": [
			    {
			      "row": 12,
			      "field": "salary",
			      "reason": "Invalid currency format"
			    }
			  ]
			}
One row failing does not stop the batch.

## Predictable File Naming Strategy

Format:

      <CandidateName>_<TemplateName>_<YYYYMMDD>.pdf


Sanitized:

1. Remove special characters
2. Trim whitespace
3. Handle duplicates

## Reliability & Scale Considerations

To support thousands of rows:
1. Streaming row processing
2. Background job queue
3. Retry mechanism per row
4. Timeout isolation
5. Progress tracking
6. Memory-safe document rendering

## Security Considerations

1. Secure storage of uploaded documents
2. Temporary file cleanup
3. Encrypted Google Sheet access tokens
4. Role-based template access
5. No document data used for AI model training

## Why GenAI Is Valuable Here

### Without AI:

1. Users must manually insert placeholders.
2. Field detection is tedious.

### With AI:

1. Automatic field detection reduces setup friction.
2. Schema inference improves UX.
3. Optional block detection enables smarter templates.

### AI is used only during:

1. Template creation
2. Schema suggestion

Rendering remains deterministic.

## Conclusion

This system combines:

1. Deterministic rendering for reliability
2. AI-assisted schema detection for usability
3. Strong validation for bulk stability
4. Clean reporting for operational transparency

The result is a practical, scalable document automation platform that reduces manual effort while maintaining formatting integrity and enterprise reliability

## Problem 4: Architecture Proposal for 5-Min Character Video Series Generator

We want to build a system that helps a user create a short video series (around **5 minutes per episode**) using predefined characters. The user defines characters (image + personality) and relationships once, then provides a short story/situation for an episode. The system outputs a complete episode package and optionally a final video. [READ MORE ABOUT THE PROJECT](./char-based-video-generation.md)

### **Task**

Create a **small, clear architecture proposal** (no code, no prompts) describing how you would design and build this system.

### Your Solution for problem 4:

## Problem recap:
Our task is to design a small, clear, production-oriented architecture proposal that directly maps to all evaluation criteria of the given problem.

## 1. High-Level Design Goals

* Consistency first: Characters, personalities, voices, and relationships must remain stable across episodes.
* Controlled creativity: New stories without breaking the series rules.
* Predictable duration: Episodes reliably fit ~5 minutes.
* Iterative workflow: Users can review, edit, and regenerate parts.
* Production-ready outputs: Scripts, assets, and final video or render package.

## 2. System Architecture Overview

				 Series Bible (Persistent State)
						        ↓
						  Episode Request
						        ↓
					Planning & Validation Layer
						        ↓
						Script & Scene Generator
						        ↓
						Asset Planning Layer
						        ↓
						User Review & Fix Loop
						        ↓
						Rendering / Export
						        ↓
						Episode Output + Report
						

## 3. Core Components

### 3.1 Series Bible (Single Source of Truth)

Stored as structured JSON and reused across episodes.

Contains:

* Character definitions (visual, personality, voice)
* Relationship rules
* World/setting constraints
* Tone & style rules

The Series Bible is never regenerated automatically — only edited by the user


### 3.2 Episode Request Layer

User provides:

* Short story prompt (situation + conflict + goal)
* Characters to include (subset allowed)
* Style (comedy, drama, etc.)
* Constraints (language, narration/dialogue, format)

This is treated as intent, not final output.


## 4. JSON Schema Design

### 4.1 Character Schema

			{
			  "character_id": "alex",
			  "visual": {
			    "reference_images": ["alex_v1.png"],
			    "notes": "Blue hoodie, short hair, mid-20s"
			  },
			  "personality": {
			    "traits": ["curious", "sarcastic"],
			    "speech_style": "casual, short sentences",
			    "behavior_rules": ["avoids confrontation"]
			  },
			  "voice": {
			    "tone": "calm",
			    "pace": "medium"
			  }
			}

### 4.2 Episode Plan Schema (Key Control Layer)

			{
			  "episode_id": "ep_05",
			  "target_duration_sec": 300,
			  "characters_used": ["alex", "maya"],
			  "scenes": [
			    {
			      "scene_id": "scene_1",
			      "purpose": "setup",
			      "estimated_duration_sec": 60,
			      "participants": ["alex"]
			    }
			  ],
			  "ambiguities_detected": false
			}

## 5. Prompt Quality Strategy (Conceptual, No Prompts)

* Zero-shot, schema-locked generation

* LLMs:

  * Read from Series Bible JSON
  * Write only to episode-specific JSON

* Explicit rules:

  * Do not invent character traits
  * Do not break relationships
  * Do not exceed scene duration budget

* If conflict exists → flag ambiguity instead of guessing

This minimizes hallucination and drift.


## 6. Script & Asset Planning

Using the validated Episode Plan, the system generates:

* Scene-by-scene script with dialogues
* Narration vs dialogue split
* Shot list / storyboard outline
* Visual asset prompts per scene
* Audio plan (voice lines, narration, music cues)

All outputs reference character IDs, ensuring consistency.

## 7. Ambiguity Handling & User Review

Ambiguity cases:

* Character behavior conflicts
* Missing relationship context
* Unclear episode goal

System behavior:

* Sets ambiguities_detected = true
*Pauses before rendering
*Highlights issues for user review

Users can:

* Edit the prompt
* Swap characters
* Regenerate only selected scenes

## 8. Bulk & Iteration Thinking

* Episodes are stored independently but share the same Series Bible
* Failure in one episode does not affect others
* Scene-level regeneration is supported
* Versioning allows iterative improvements

Output Structure

				series_name/
				  bible.json
				  episodes/
				    ep_01/
				      episode_plan.json
				      script.json
				      assets/
				      final_video.mp4

## 9. Reporting & Reliability

Each episode produces a generation report:

* Final duration
* Characters used
* Warnings or rule violations
* Completion status


## 10. Conclusion

This design treats characters and relationships as immutable state, episodes as planned variations, and AI as a controlled writer rather than a free creator.
It enables consistent, repeatable generation of 5-minute character-based episodes while remaining scalable, review-safe, and production-ready.





