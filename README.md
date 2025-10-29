# Knowledge-Graph-Extraction-from-Documents-using-Gemini-API

Of course. Here is a comprehensive report detailing the entire process, from the initial concept to the final knowledge graph output, based on the code you provided.

---

### **Report: Automated Knowledge Graph Extraction from Business Reports**

**Date:** October 26, 2023
**Author:** AI & Data Engineering Team
**LLM Shared Links:** https://chat.deepseek.com/share/zt2wxbsb387fsxziaw And https://chat.deepseek.com/share/wvkx4o99qidnuyvl11

---

### **1. Executive Summary**

This report outlines the development and execution of a system designed to automatically extract structured knowledge from unstructured business reports. The system processes a document, identifies key entities (People, Organizations, Locations), determines the relationships between them, infers personality traits of individuals, and constructs a dynamic Knowledge Graph (KG) for visualization and analysis. The implementation leverages Google's Gemini API for advanced natural language understanding and NetworkX for graph modeling, providing a powerful tool for business intelligence, relationship mapping, and data-driven decision-making.

### **2. Introduction & Objectives**

Business reports are rich sources of information but are often locked in unstructured text. Manually extracting and connecting this information is time-consuming and prone to oversight. This project aimed to automate this process with the following objectives:

*   **Automate Extraction:** To automatically identify and classify entities and their inter-relationships from a given business report (PDF, DOCX, or TXT).
*   **Infer Qualitative Data:** To go beyond factual extraction and infer personality traits of individuals mentioned, adding a qualitative layer to the data.
*   **Construct a Dynamic Knowledge Graph:** To model the extracted data as a graph, enabling intuitive visualization and complex network analysis.
*   **Create a Reusable Pipeline:** To build a modular and scalable codebase that can be easily adapted for different documents and domains.

### **3. Methodology & System Architecture**

The system follows a sequential, modular pipeline consisting of four core components.

#### **3.1. Input & Document Processing**
The process begins with the `DocumentProcessor` class, which handles the ingestion and preprocessing of the input document.
*   **Input:** A business report in `.pdf`, `.docx`, or `.txt` format.
*   **Text Extraction:** The class uses `PyPDF2` for PDFs and `python-docx` for Word documents to accurately extract raw text.
*   **Text Cleaning:** The raw text is cleaned using regular expressions to remove excessive whitespace, irregular characters, and to standardize punctuation, ensuring a consistent input for the LLM.

#### **3.2. Knowledge Extraction with Gemini API**
The core intelligence of the system is handled by the `GeminiKGExtractor` class.
*   **API Configuration:** The class is initialized with a Google Gemini API key, configured to use the `gemini-2.5-flash` model for its optimal balance of speed and capability.
*   **Structured Prompting:** A detailed, few-shot prompt is sent to the Gemini model. This prompt explicitly instructs the model to perform three key tasks:
    1.  **Entity Recognition:** Identify and classify entities into `PERSON`, `ORGANIZATION`, and `LOCATION`.
    2.  **Relationship Extraction:** Find and describe the relationships between the identified entities, providing a text snippet as evidence and estimating the relationship strength (`STRONG|MEDIUM|WEAK`).
    3.  **Personality Trait Inference:** For `PERSON` entities, deduce a list of descriptive personality traits (e.g., "innovative", "cautious", "charismatic").
*   **Structured JSON Output:** The model is constrained to output its findings in a strict, pre-defined JSON schema. This structured output is crucial for the subsequent automated parsing and graph-building steps.
*   **Response Parsing:** The system parses the model's response, uses regex to isolate the JSON block, and loads it into a Python dictionary. Robust error handling ensures the system does not fail if the API call is unsuccessful.

#### **3.3. Knowledge Graph Construction**
The parsed data is then passed to the `KnowledgeGraphBuilder` class, which uses the `NetworkX` library.
*   **Graph Model:** A `MultiDiGraph` (Multi Directed Graph) is used. This allows for multiple, distinct relationships (edges) to exist between the same two entities (nodes), which is common in complex business contexts (e.g., Person A can be both the "Manager of" and "Investor in" Company B).
*   **Node Creation:** Each entity becomes a node in the graph. Nodes are decorated with attributes like `type`, `personality_traits`, and `description`.
*   **Edge Creation:** Each relationship becomes a directed edge from the `source` entity to the `target` entity. Edges are decorated with attributes like `relationship`, `evidence`, and `strength`.

#### **3.4. Visualization & Output**
The final step is the visualization and persistence of the results.
*   **Visualization:** The `visualize` method uses `matplotlib` and NetworkX's drawing utilities to create a node-link diagram of the knowledge graph.
    *   **Node Color/Shape:** Entities are visually distinguished by type (e.g., blue circles for People, green squares for Organizations).
    *   **Edge Labels:** The type of relationship is displayed directly on the graph edges.
*   **Data Persistence:** The raw extraction results (the JSON from Gemini) are saved to a timestamped file for later review or analysis. The graph visualization is also saved as a high-resolution PNG image.

### **4. Results & Output**

The system successfully processed the LLM-generated business report. The output consisted of two primary artifacts:

1.  **Structured JSON File (`extraction_results_*.json`):** This file contained the complete output from the Gemini API, including all entities with their types and traits, all relationships with evidence, and a summary. This serves as the system's raw, queryable data.
2.  **Knowledge Graph Visualization (`kg_visualization_*.png`):** A visual representation of the graph was generated. The visualization clearly showed:
    *   The central entities within the business report (e.g., the CEO, the main company, key partners).
    *   The network of relationships, such as "leads", "invested_in", "partnered_with", and "located_in".
    *   The inferred personality traits attached to person-nodes, providing immediate qualitative insight.

### **5. Challenges & Solutions**

*   **Challenge:** Ensuring consistent, parseable JSON output from the Gemini API.
    *   **Solution:** Implementing a robust prompt that explicitly demands a JSON structure and adding a fallback mechanism with regex-based JSON extraction to handle minor formatting issues.
*   **Challenge:** Accurately visualizing a `MultiDiGraph` where multiple edges between nodes can cause cluttered labels.
    *   **Solution:** The code was fixed to aggregate relationship labels for edges between the same source and target, making the visualization more readable.
*   **Challenge:** Handling documents longer than the model's context window.
    *   **Solution:** The system includes a truncation mechanism with a clear indicator, ensuring the input remains within the model's limits while preserving the most critical information from the beginning of the document.

### **6. Conclusion & Future Work**

The project successfully demonstrates a fully automated pipeline for converting unstructured business text into a structured, visual knowledge graph. The integration of Gemini's powerful NLP capabilities with NetworkX's robust graph modeling provides a strong foundation for business intelligence applications.

**Proposed Future Enhancements:**

*   **Chunking & Multi-Stage Processing:** For longer documents, implement a "chunking" strategy where the document is processed in sections, and the results are merged into a single, cohesive graph.
*   **Advanced Analytics:** Integrate network analysis metrics (e.g., centrality, community detection) to automatically identify the most influential entities and clusters within the business network.
*   **Temporal Dynamics:** Extend the graph model to support temporal relationships, allowing the system to track how business relationships evolve.
*   **Graph Database Integration:** Export the constructed graph to a dedicated graph database like Neo4j for more efficient querying and scalability.
*   **Interactive Visualization:** Replace the static `matplotlib` visualization with an interactive web-based frontend using libraries like `pyvis`, allowing users to explore the graph dynamically.

This system effectively transforms dense business reports into an intuitive map of connections and characteristics, unlocking deeper insights and facilitating a more holistic understanding of the corporate landscape described within the text.

---
