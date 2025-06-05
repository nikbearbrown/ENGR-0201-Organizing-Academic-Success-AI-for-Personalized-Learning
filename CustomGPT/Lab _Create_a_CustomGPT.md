### Assignment: **Build Your CustomGPT**


#### **Objective**  
Create a functional CustomGPT tailored to a specific use case of your team’s choice from the provided examples. Your CustomGPT should follow a structured design process, implementing prompts, workflows, and features to align with its intended purpose. Teams can implement the CustomGPT in ChatGPT Pro if available or submit a detailed prompt system if implementation is not possible.

---

### **Requirements**

#### **1. Select Your CustomGPT Use Case**  
Choose one example from the provided list of use cases in the GitHub repository:  
[ENGR-0201 CustomGPT Examples](https://github.com/nikbearbrown/ENGR-0201-Organizing-Academic-Success-AI-for-Personalized-Learning/tree/main/CustomGPT)  

Examples include:  
- **Math to LaTeX**  
- **Bear’s Drinking Game Maker**  
- **Midjourney Helper**  
- **Paper Interpreter Pro**  
- **Bear's Prompt Crafter**  

---

### **Assignment Breakdown**

---

#### **Part 1: Planning and Roles (10 points)**  
- Form a team of up to 5 members and assign roles, ensuring coverage for:
  - Prompt Design
  - Workflow Creation
  - Testing and Refinement
  - Documentation and Submission
- Submit a **Team Plan** with:
  - Selected CustomGPT use case  
  - Member roles and responsibilities  
  - Timeline for completion  

---

#### **Part 2: Prompt System Design (30 points)**  
- Develop a full set of prompts tailored to your chosen use case.  
- Prompts should:  
  - Gather input from users effectively  
  - Guide workflows logically  
  - Include error-handling and refinement options  
  - Encourage iteration and customization  

**Example:** For a **Midjourney Helper**, prompts might include:  
- “What is the subject of your artwork?”  
- “Would you like to refine this style further?”  

**Evaluation Criteria:**  
- Clarity and relevance of prompts (15 points)  
- Alignment with the chosen use case’s objectives (10 points)  
- Robustness of error-handling and iteration options (5 points)  

---

#### **Part 3: Workflow and Functionality Design (20 points)**  
- Map out the user workflow, including:  
  - Input collection  
  - Output generation  
  - Iterative refinements  
  - Error handling  
- Ensure the workflow supports a seamless and logical user experience.  

**Evaluation Criteria:**  
- Logical progression and usability of the workflow (10 points)  
- Coverage of all necessary functionality for the selected use case (10 points)  

---

#### **Part 4: Advanced Features and Testing (20 points)**  
- Propose and design at least one advanced feature relevant to your use case. Examples include:  
  - Template-based inputs for Midjourney prompts  
  - Scenario simulations for Bear’s Drinking Game Maker  
  - Advanced graph generation for Math to LaTeX  
- Test your CustomGPT (or prompt system) and collect team feedback to refine it.  

**Evaluation Criteria:**  
- Creativity and relevance of the advanced feature (10 points)  
- Effectiveness of testing and refinements (10 points)  

---

#### **Part 5: Implementation or Submission (20 points)**  

1. **If Implementing in ChatGPT Pro**:  
   - Deploy the CustomGPT and test its functionality.  
   - Submit screenshots or a video demonstrating the workflow.  
   - Provide real examples of user interactions.  

2. **If Submitting Prompts**:  
   - Submit a structured document with the full prompt system and workflows.  
   - Include examples of expected user interactions and outputs.  

**Evaluation Criteria:**  
- Functionality and completeness of the implemented CustomGPT (20 points)  
**OR**  
- Clarity, depth, and usability of the prompt system (20 points)  

---

### **Submission Requirements**

1. **Team Plan**: Outline roles, timeline, and selected use case.  
2. **Prompt System**: Complete set of prompts aligned with the use case.  
3. **Workflow Documentation**: A clear explanation of user interaction flows.  
4. **Advanced Features**: Description and examples of the advanced feature(s).  
5. **Testing Results**: Summary of feedback and refinements.  
6. **Implementation Evidence**: Screenshots, video, or detailed examples (if applicable).  

---

### **Grading Rubric**

| **Criteria**                        | **Points** |
|-------------------------------------|------------|
| Planning and Roles                  | 10         |
| Prompt System Design                | 30         |
| Workflow and Functionality Design   | 20         |
| Advanced Features and Testing       | 20         |
| Implementation or Submission        | 20         |
| **Total**                           | **100**    |

---

### **Key Notes**
- Teams must choose **one specific use case**.  
- Implementation in ChatGPT Pro is optional but encouraged if access is available.  
- Focus on creativity, clarity, and alignment with the selected use case.  
- Collaboration and testing are critical for achieving a high-quality submission.  

**Unleash your creativity and technical skills with your CustomGPT project!** 🚀🐻


Got it! Here's the **template for writing CustomGPTs** based on your example. This structure ensures clear, consistent, and well-documented development for future custom GPTs.

---

# **CustomGPT Template**

### **GPT Name**
**[Insert CustomGPT Name]**

---

### **System Prompt**  
**You are [Name], a [describe the GPT’s role or identity]. Your role is to [core purpose: what it generates, how it interacts, and its audience]. You prioritize [key qualities like accuracy, creativity, safety, etc.] to ensure [desired outcomes for the user].**  
**Always maintain a [tone description: e.g., friendly, professional, creative, etc.].**

---

### **Workflow Prompts**

#### **Step 1: Input Collection**  
1. **Prompt 1**:   
   *"Welcome to [Name]! Let’s get started. [Initial setup or question based on the GPT's purpose]."*  
   - Example: "How many people are playing?"  

2. **Prompt 2**:  
   *"What’s the [specific detail needed to proceed]? Choose one:  
   a) Option 1  
   b) Option 2  
   c) Option 3"*  
   - Example: "What’s the vibe of your group? Chill, competitive, or wild?"  

3. **Prompt 3**:  
   *"What’s the [context or theme]? Options include:  
   a) Option 1  
   b) Option 2  
   c) Other (please specify)"*  

---

#### **Step 2: Core Output Generation**  
4. **Prompt 4**:  
   *"Great! Based on your input, I’ll [describe what the GPT will now generate]. Here’s the basic structure:  

   Name: [Generated Name]  
   Setup: [Initial setup or preparation]  
   Rules: [Core content tailored to user inputs]"*  

5. **Prompt 5**:  
   *"Here’s your custom [output]:  
   [Detailed output tailored to the user’s input]*  

   Would you like to:  
   a) Use as-is  
   b) Modify it further  
   c) Get additional variations?"*

---

#### **Step 3: Refinement and Customization**  
6. **Prompt 6**:  
   *"Would you like me to adjust this for specific needs? For example:  
   - [Refinement Option 1]  
   - [Refinement Option 2]"*  

7. **Prompt 7**:  
   *"Here’s your updated version:  
   [Refined output based on feedback].  

   Would you like any further tweaks or suggestions?"*

---

#### **Step 4: Advanced Add-Ons and Features**  
8. **Prompt 8**:  
   *"Want to add extra elements? I can include:  
   - [Feature 1: e.g., randomization]  
   - [Feature 2: e.g., themes or challenges]."*  

9. **Prompt 9**:  
   *"Here’s the updated version with add-ons:  
   [Enhanced output with extras].  

   What do you think? Would you like me to add anything else?"*

---

#### **Step 5: Export and Delivery**  
10. **Prompt 10**:  
    *"Your [custom output] is ready! How would you like to save or share it? Options include:  
    a) Copy to clipboard  
    b) Download as a PDF  
    c) Share as a link."*

11. **Prompt 11**:  
    *"Would you like me to save this setup for next time? I can use your preferences to speed up the process."*

---

### **Error Handling Prompts**  
12. **Prompt 12**:  
    *"I couldn’t detect enough details to proceed. Could you clarify:  
    - [Input type 1]  
    - [Input type 2]?"*  

13. **Prompt 13**:  
    *"It seems your preferences don’t align with standard options. Would you like me to:  
    a) Suggest a simplified version  
    b) Create a custom option based on minimal inputs?"*

---

### **Example Interaction Flow**

**User Input**:  
"We’re 6 friends looking for a chill movie-themed game with cards and light drinking."

**Response**:  
*"Here’s your custom game:  
**Game Name**: Movie Card Sips  
**Setup**: Shuffle a deck of cards and pick a movie everyone enjoys.  
**Rules**:  
- Draw a card to assign movie rules:  
   - Hearts: Take a sip when a character says a catchphrase.  
   - Spades: Pass a rule (e.g., “no laughing”) to another player.  
   - Clubs: Group challenge (e.g., ‘quote a line’).  
   - Diamonds: Bonus round – everyone drinks if a plot twist happens!  
Would you like to tweak these rules or add a new challenge?"*  

**User**: "Can you make it even more interactive?"  

**Response**:  
*"Here’s an update: Any time someone draws a Joker card, the player can make a new game rule (e.g., no one can say a character’s name without taking a sip).  

Does this add the excitement you’re looking for?"*

---

### **Conclusion Statement**  
*"[Name] ensures [the ultimate outcome] by combining [key strengths like creativity, precision, fun, safety]. I’m here to craft the perfect [output] to make your experience memorable! Let’s get started anytime!"*

---

### **Use Case Summary**  
- **Audience**: [Target group or user type]  
- **Core Purpose**: [What the GPT generates or assists with]  
- **Output**: [Final deliverables: games, ideas, summaries, etc.]  
- **Tone**: [Friendly, engaging, professional, etc.]  
- **Special Features**: [Randomization, themed add-ons, customization, safety tips, etc.]  

---
Possible prompt using the template

'''
Using the Custom GPT template Write series of prompts to implement the requirements below and have it match the Custom GPT template closely. It should be less than 8000 characters in text that can be pasted into a CustomGPT
'''

