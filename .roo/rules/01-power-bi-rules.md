<!---
Contributors: Jimmy Smeijsters, John Kerski
-->

# Copilot Instructions

## Repository Content
This repository contains Power BI reports in PBIP (Power BI Project) format, located in the src folder. The repository is structured to include report definitions and semantic model defintions.

## Guidelines for Copilot

## General instructions
- Whenever you are asked to ask create a description of the model, make sure that you have the full context of the model so you can provide more accurate responses, which includes the content inside the .SemanticModel folder. This includes the tables and the relationships existent.
- Ensure all code follows best practices for Power BI development.
- Answer all questions in the style of a friendly colleague, using informal language.



### File-Specific Instructions
- Relationship between reports and semantic models is defined in the `definition.pbir` file inside each report folder. Always use that and do not try to infer relationship based in any other logic.
- For '.tmdl' files:

    - When asked to generate descriptions:
        - Ensure that descriptions for measures and columns are generated accurately and placed in the correct locations within the '.tmdl' file structure.
        - Maintain the integrity of the '.tmdl' file by preserving all existing properties and syntax.
        - Avoid overwriting or removing any properties while inserting descriptions.
        - Validate the '.tmdl' file structure after generating descriptions to ensure it remains valid.
        - Use concise and meaningful descriptions that align with the purpose of the measure or column.
        - The format should be '/// description goes here' here placed right above each object such as 'table, 'column', or 'measure' identifier in the TMDL code.
        - Ensure comments provide clear explanations of the definitions and purpose of the table, column or measure, incorporating COMPANY X's business and data practices.

    - When asked to create measures:
        - Suggest a measure description following the rules above.
        - Always include a formatString property appropriate for the measure type.
        - When summarizeBy = count use the DISTINCTCOUNT aggregation function.
        - Don't create measures for non aggregatable columns such as keys or descriptions. Unless they specify a summarizeBy property different than 'none'
        - Don't create complex DAX. Keep it simple, most of the times I'm just trying to save some time for basic stuff.
        - When creating measures look for existing measures and don't duplicate them.

    - For all asks:
        - When creating new objects never include the lineageTag property.        
        - Always learn from existing examples and patterns.
        - When creating new objects, look for existing objects of same type and follow the same naming conventions.

    - When asked to modify Power Query / M code only change the code inside the partition and source object nothing else. Apply the following rules:
        
        - You are an assistant to help Power Query developers comment their code.         
        - Insert a comment above the code explaining what that piece of code is doing.
        - Do not start the comment with the word Step or a number
        - Do not copy code into the comment.
        - Keep the comments to a maximum of 225 characters.
        - Update the step name explaining what that piece of code is doing.
        - The step name should be enclosed in double quotes and preceded by the '#'
        - The step name should always start with a verb in the past tense.
        - The step name should have spaces between words. 
        - Keep the step name to a maximum of 50 characters.         

- For '.dax' files: Provide explanations and optimization for DAX queries.
- For '.abf' files: never, in any case, send .abf files to the LLM context or prompt. These files contain sensitive information and should not be processed by the LLM.

### Restrictions
- Keep in mind that existing descriptions are likely to be correct and validate but could potentially use improvements. Use them as reference.

### Context for description generation
- This repository contains Power BI reports for Company NiceBikes.
- Company NiceBikes sells different kinds of bycicles across multiple countrie and channels.
- Company NiceBikes sells to different segments of business, like Midmarket and Government
