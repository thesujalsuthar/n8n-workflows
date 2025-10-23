# AI-Powered Sustainable Packaging Recommendation & Inventory Optimization Workflow

This workflow leverages AI-enabled analysis to recommend sustainable packaging materials and optimize inventory orders by minimizing carbon footprint and cost. It integrates data from multiple sources and produces actionable recommendations that balance eco-friendliness and supplier availability.

---

## Workflow Overview

The workflow consists of the following key steps:

1. **Fetch Packaging Materials Data**  
   Retrieves current packaging materials details from an external API endpoint.

2. **Fetch Carbon Footprint Data**  
   Obtains carbon footprint values per unit for various packaging materials.

3. **Fetch Supplier Inventory Data**  
   Collects supplier inventory availability and cost information for packaging materials.

4. **Generate Recommendations & Optimize Orders**  
   Processes the collected data to:  
   - Map packaging materials to their carbon footprints.  
   - Identify eco-friendly alternatives (same type, lower carbon footprint).  
   - Cross-reference supplier inventories for availability and cost.  
   - Optimize order quantities to minimize combined carbon impact and cost.  

5. **Save Recommendations as Document** *(currently inactive)*  
   Saves the final recommendations as a Google Docs document with a timestamped title.

---

## Nodes Description

### 1. Start  
- Trigger node to initiate the workflow.

### 2. Fetch Packaging Materials Data  
- HTTP GET request to `https://api.example.com/packaging-materials`  
- Retrieves list of materials, including ids, names, types, and cost per unit.

### 3. Fetch Carbon Footprint Data  
- HTTP GET request to `https://api.example.com/carbon-footprints`  
- Fetches carbon footprint data (carbon emissions per unit) associated with material IDs or names.

### 4. Fetch Supplier Inventory Data  
- HTTP GET request to `https://api.example.com/supplier-inventories`  
- Obtains available quantities and cost per unit from suppliers for packaging materials.

### 5. Generate Recommendations & Optimize Orders  
- Custom JavaScript Function node that performs the core logic:  
   - Creates a mapping between materials and their carbon footprints.  
   - Finds eco-friendly alternatives for each material (matching type, lower carbon footprint).  
   - Checks supplier availability and pricing for each alternative.  
   - For each material and candidate alternative, scores options based on weighted carbon footprint (70%) and cost (30%).  
   - Recommends optimal order quantities (up to a default desired quantity of 100 units) based on availability and scoring.  
- Outputs an array of recommendation objects including:  
   - Original material info  
   - Recommended material ID and quantity  
   - Estimated carbon footprint and cost for the optimized order  

### 6. Save Recommendations as Document *(Disabled)*  
- Intended to save the recommendations as a Google Docs document in the specified folder.  
- Document content is a pretty-printed JSON string of the recommendations.  
- Document title includes the current date (ISO format).  
- Currently, this node is inactive and must be enabled manually if saving output documents is desired.

---

## Data Flow and Connections

- The **Start** node triggers parallel HTTP requests to gather materials, carbon footprints, and supplier inventory data.  
- All data streams feed into the **Generate Recommendations & Optimize Orders** function node.  
- The function node processes the combined data and outputs the recommendation results.  
- These results are passed to the **Save Recommendations as Document** node (currently inactive), which can archive the recommendations.

---

## Usage Notes

- The workflow assumes availability and accurate responses from the specified API endpoints.  
- Configuration for folder ID and credentials for Google Docs should be set up prior to activating the document save node.  
- The optimization algorithm uses a simple weighted heuristic to balance environmental impact and cost; weights can be adjusted in the function node code.  
- Desired order quantities and other parameters can be customized within the function node.

---

## Example Output (Excerpt)

```json
[
  {
    "materialId": "mat123",
    "materialName": "Recycled Cardboard",
    "recommendedMaterialId": "mat456",
    "recommendedQuantity": 80,
    "estimatedCarbonFootprint": 240,
    "estimatedCost": 320
  },
  ...
]
```

---

This workflow enables companies to make data-driven decisions by recommending sustainable packaging options while optimizing inventory orders to reduce environmental impact and cost.