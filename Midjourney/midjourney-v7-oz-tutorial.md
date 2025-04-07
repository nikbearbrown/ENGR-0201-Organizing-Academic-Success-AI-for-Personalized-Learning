# Creating a Modern Oz Manga: Ultimate Midjourney V7 Tutorial

This comprehensive tutorial will guide you through using Midjourney V7's powerful new features to create a manga-style storyboard for an animated film featuring a 27-year-old Dorothy returning to Oz. We'll leverage V7's Draft Mode, character creation, voice commands, and style flexibility to rapidly build a cohesive visual narrative.

## Setting Up Your Midjourney V7 Workspace

### Activating Personalization
1. **Enable personalization** (now on by default but requires 5-minute unlock)
2. **Toggle it on/off** using the black/red "P" square icon
3. **Rate images** to refine your global style profile
4. **Create mood boards** for specific manga aesthetics

#### Personalization Command Examples:
```
/settings
```
Then select the personalization toggle. The P icon will be red when active.

### Understanding Draft Mode for Rapid Manga Panel Creation
Draft Mode is your secret weapon for quickly iterating manga panels, allowing you to make changes in seconds rather than minutes.

#### Basic Draft Mode Commands:
```
/draft
```
or
```
/imagine draft: a modern 27-year-old woman standing at the edge of a wheat field, manga style
```

#### Setting Aspect Ratios for Manga Panels:
```
ar 16:9
```
or
```
square aspect ratio
```
or
```
ar 3:4 (standard manga page ratio)
```

## Character Design: Creating Your Modern Oz Cast

### The Importance of Combined Names
When creating characters in Midjourney V7, always use combined names without spaces (e.g., DorothyGale instead of Dorothy or Dorothy Gale). This prevents several issues:

1. **Avoids confusion with common words** in prompts
2. **Differentiates from Midjourney's existing knowledge** of characters
3. **Prevents overlap with other users'** similarly named characters
4. **Creates a unique identifier** that won't be misinterpreted

### Defining Adult Dorothy

Start by creating a character profile for your 27-year-old Dorothy that focuses on her attributes without specifying a style:

```
/imagine draft: Character DorothyGale: 27 years old, confident professional meteorologist, shoulder-length auburn hair with subtle blue highlights, practical field clothes with a modern blue gingham shirt, carries a weather tablet, ruby earrings as a memento, expressive eyes showing wisdom beyond her years.
```

After generating, continue refining with nudge prompts:
```
Make her expression more determined
```
```
Add subtle scars from her first adventure
```
```
Show a small Toto-inspired dog tattoo on her wrist
```

#### Pro Tip:
Once you're happy with Dorothy's design, say:
```
Remember this character as DorothyGale
```

Now Midjourney will associate this specific character definition with "DorothyGale" during your current draft session. In future prompts, you can simply mention "DorothyGale" and MJ will recall these details.

### Creating the Evolved Oz Companions

#### Modern Scarecrow:
```
/imagine draft: Character ScarecrowBrain: evolved intelligent advisor, wears spectacles and formal attire with straw accents, carries digital tablets and ancient scrolls, has developed a more human-like face but retains straw hair and stitched features. Academic posture.
```

#### Modern Tin Woodman:
```
/imagine draft: Character TinmanHeart: steampunk-inspired metal body with visible heart mechanism, polished chrome with copper accents, emotional eyes, carries an ornate axe with magical enhancements, decorative engravings showing his life story.
```

#### Modern Lion:
```
/imagine draft: Character LionCourage: regal posture, battle-scarred warrior, wears minimal golden armor on shoulders and chest, flowing mane styled with beads and small braids, confident expression, ceremonial weapon, stands upright.
```

#### Remember each character:
After creating each design you're happy with, remember to say:
```
Remember this character as [combined character name]
```

**IMPORTANT:** Always use combined names without spaces (ScarecrowBrain, TinmanHeart, LionCourage) to ensure Midjourney properly distinguishes your specific character designs.

### Character Memory Limitations

It's crucial to understand how Midjourney V7's character memory works:

1. **Session-based memory:** Characters are only remembered within a single draft session
2. **Lost on page reload:** Starting a new draft session or reloading the page clears character memory
3. **Documentation needed:** For multi-day projects, keep a document with all character definitions
4. **Consistency challenges:** You may need to redefine characters frequently

For long-term projects, create a reference document containing:
- Complete character definition prompts
- URLs of successful character images
- Notes on refinements that worked well

## Scene Creation: Key Locations in Your Modern Oz

### The Kansas Farm - Dorothy's Starting Point:
```
/imagine draft: modern Kansas farm with advanced weather monitoring equipment, drought-resistant crops, old farmhouse with technological updates, stormy skies approaching on the horizon, golden wheat fields. Environmental storytelling with dramatic perspective.
```

### The New Yellow Brick Road:
```
/imagine draft: deteriorated yellow brick road with high-tech brick replacements in some sections, magical plants growing between cracks, abandoned scarecrow warning posts, strange mechanical animals watching from distance, perspective view showing the road curving toward an evolved Emerald City skyline.
```

### Evolved Emerald City:
```
/imagine draft: Emerald City reimagined as bio-mechanical metropolis, crystal spires mixed with organic architecture, floating transportation platforms, magical energy flowing through transparent conduits, citizens with diverse fantasy appearances. Detailed establishing shot with vertical composition.
```

## Applying Different Styles to Your Characters

One of Midjourney V7's most powerful features is the ability to apply different artistic styles to your defined characters. Once you've defined a character, you can render them in countless visual styles without redefining them.

### Manga Style Variations:

#### Shōnen Action Style:
```
/imagine draft: DorothyGale in shonen manga style, dynamic pose, action lines, bold inking, dramatic lighting, high contrast, expressive features
```

#### Seinen Mature Style:
```
/imagine draft: DorothyGale in seinen manga style, realistic proportions, detailed cross-hatching, subtle expressions showing complex emotions, atmospheric lighting, muted palette
```

#### Shōjo Expressive Style:
```
/imagine draft: DorothyGale in shojo manga style, flowing hair, decorative elements, flower motifs, sparkle effects, expressive eyes, emotional symbolism, delicate linework
```

### Photorealistic Styles:

#### Cinematic Realism:
```
/imagine draft: DorothyGale in photorealistic style, natural lighting, high detail facial features, cinematic composition, shallow depth of field, 8K digital photography
```

#### Concept Art Style:
```
/imagine draft: DorothyGale in concept art style, professional lighting, detailed texturing, character design focus, studio background, presentation quality
```

### Style Reference Options

There are two main methods to maintain style consistency:

#### 1. Style Reference (-sref)
Use `-sref` to reference a specific image's style (not `-cref` which doesn't work in V7):
```
/imagine draft: DorothyGale in stormy field --sref [URL of image with desired style]
```

#### 2. Personalization (-p)
Use personalization to apply your global style preferences:
```
/imagine draft: DorothyGale in stormy field -p 100
```
The number after `-p` controls personalization strength (1-100).

#### Combined Approach:
You can combine both methods for greater control:
```
/imagine draft: DorothyGale in stormy field --sref [URL of image with desired style] -p 60
```

## Storyboarding Techniques in Draft Mode

### Creating Sequential Panels

Start with an establishing shot:
```
/imagine draft: DorothyGale standing at Kansas farm border during approaching storm, wind blowing her hair, clutching tablet showing weather anomalies, looking concerned toward darkening sky. Wide manga panel, dramatic lighting.
```

Then iterate with sequential panels through nudge prompts:
```
Show closeup of DorothyGale's determined expression
```
```
Pull back to show storm forming a magical portal above
```
```
Show DorothyGale being lifted by swirling magic winds
```
```
DorothyGale falling through dimensional tunnel, surrounded by fragments of memories from her childhood
```
```
DorothyGale landing in outskirts of evolved Oz, disoriented
```

### Using Voice Commands for Dynamic Scene Creation

Enable your microphone in Draft Mode and try voice directing scenes like:

*"Show DorothyGale discovering a broken section of yellow brick road with strange magical energy coming from beneath the cracks"*

*"Pull back to a wide shot showing DorothyGale being watched by mechanical crows perched on dead trees"*

*"Cut to DorothyGale hearing rustling in the fields and turning quickly, her hand reaching for her weather tablet as a defense tool"*

*"Pan across to reveal ScarecrowBrain emerging from the fields, looking very different than she remembers"*

*"Create a split panel showing DorothyGale's shocked expression on one side and ScarecrowBrain's uncertain recognition on the other"*

## Advanced Character Interaction Scenes

### Character Meeting Composition:
```
/imagine draft: DorothyGale and ScarecrowBrain reunion in wheat field, emotional moment, DorothyGale looking shocked at his evolved appearance, ScarecrowBrain adjusting spectacles in disbelief, magical particles floating around them signifying Oz recognizing DorothyGale's return. Manga-style emotional indicators, dynamic panel composition.
```

Then use combined character names for quick iterations:
```
DorothyGale stepping forward cautiously
```
```
ScarecrowBrain offering a book showing Oz's history since she left
```
```
Add LionCourage watching from distance
```

### Action Sequence Storyboarding:
```
/imagine draft: DorothyGale, ScarecrowBrain and LionCourage fleeing from mechanical storm crows, dynamic running poses, debris flying, LionCourage protecting others, DorothyGale using weather tablet to analyze attack pattern, multiple sequential action lines, manga speed effects. High-energy panel composition.
```

## Panel Layout Techniques

### Dynamic Action Layout:
```
/imagine draft: manga page layout with 5 panels: 1) DorothyGale discovering approaching danger (top wide panel), 2-3) reaction shots of companions (small squares), 4) enemies appearing (medium panel), 5) DorothyGale taking defensive stance (large bottom panel). Clear gutters between panels, manga reading direction indicators.
```

### Emotional Focus Layout:
```
/imagine draft: manga page with focus panel structure: central large panel showing DorothyGale's emotional realization that Oz has changed forever, surrounded by smaller memory panels showing original Oz experiences, decorative frame elements connecting panels, thought bubbles, emotional indicators.
```

## Adding Manga Text Elements

### Creating Text Bubbles and Effects:
```
/imagine draft: manga panel with DorothyGale speaking to companions, empty speech bubbles of various shapes indicating different emotional tones, thought bubbles, action sound effects text in stylized English and Japanese onomatopoeia, motion lines, emotional symbols. Clean layout with space for dialogue.
```

## Building a Cohesive Visual Narrative

### Creating Style Consistency Across Panels

For manga style consistency, use Style References:
```
/imagine draft: DorothyGale confronting mechanical witch --sref [URL of your favorite manga panel]
```

For personalized styles:
```
/imagine draft: DorothyGale confronting mechanical witch -p 70
```

Remember that if you're shifting between styles (photorealistic concept art to manga storyboards), you'll want to be explicit about the style in each prompt rather than relying solely on personalization.

### Developing Visual Character Arcs:

#### DorothyGale's Transformation Arc:
```
/imagine draft: 3-panel manga sequence showing DorothyGale's visual evolution: 1) arriving in Oz looking uncertain and out of place, 2) mid-adventure with disheveled appearance but growing confidence, 3) final form with magical elements incorporated into appearance, standing confidently with evolved companions. Progressive panel composition.
```

## Specialized Scene Types for Your Oz Manga

### Dream/Memory Sequence:
```
/imagine draft: DorothyGale's memory sequence of original Oz as child, hazy borders, desaturated colors except for ruby slippers, younger version with pigtails, simplified childlike versions of original companions, floating sepia-toned panels with wavy borders. Manga flashback indication lines.
```

### Magical Battle Sequence:
```
/imagine draft: DorothyGale using weather control abilities gained in Oz, swirling storm elements around her hands, electricity crackling, wind effects shown with dynamic lines, opponents being pushed back, impact frames, manga energy visualization techniques, speed lines, reaction cutaways.
```

### Emotional Climax Scene:
```
/imagine draft: DorothyGale making difficult choice to save Oz by combining modern technology with Oz magic, tears floating upward, magical energy surrounding her, companions reaching toward her, split lighting showing past and present simultaneously, symbolic imagery overlaid. Emotional manga climax panel with decorative borders.
```

## Exporting and Enhancing Your Storyboard

### Upscaling Key Frames:
Once you have draft panels you love, upscale them for final quality:
```
U1 (for first upscale variation)
```
or
```
/imagine [final prompt text from your draft] --v 7 --q 2
```

### Creating Transition Panels:
```
/imagine draft: transition panel showing yellow brick road morphing between different states of decay and renewal as DorothyGale travels deeper into Oz, sequential transformation, manga page turn indicator. Minimalist style with focus on transformation elements.
```

## Advanced V7 Techniques for Manga Storytelling

### Using Description Nudging for Manga Panel Development:

Start with a basic concept:
```
/imagine draft: DorothyGale discovering old ruby slipper fragments
```

Then progressively build the panel:
```
Make it a closeup manga panel
```
```
Add dramatic lighting from below
```
```
Show DorothyGale's hands carefully picking up a fragment
```
```
Add magical glow activating as she touches it
```
```
Show surprise in her eyes reflected in the ruby fragment
```

### Voice-Direct an Entire Sequence:

With microphone enabled, try describing a complete sequence:

*"Create a manga sequence where DorothyGale and companions discover the witch's mechanical factory. Start with them approaching a dark mountain. Then show them finding an entrance. Next, show their shocked reactions as they see what's inside. Then create a dramatic reveal panel of mechanical creatures being built. Finally, show them being discovered by security systems."*

## Complete Project Example: Dorothy's Return to Oz Manga Opening

Here's a complete sequence of prompts to create the opening sequence of your manga:

1. **Title Page:**
```
/imagine draft: manga title page "DOROTHY'S RETURN" with adult DorothyGale silhouette against Kansas tornado sky, emerald city visible in storm clouds, dramatic manga title typography, author credit space, professional cover composition
```

2. **Opening Panel - Kansas:**
```
/imagine draft: DorothyGale standing at farmhouse porch monitoring tablet showing storm anomaly, worried expression, practical field clothes with modern blue gingham pattern. Clean manga linework, establishing shot panel.
```

3. **Storm Development:**
```
/imagine draft: wide manga panel showing massive storm developing, DorothyGale in foreground taking readings, farm equipment being affected by strange energy, magical glints visible in clouds that only DorothyGale notices. Dramatic manga sky techniques.
```

4. **Transition to Oz:**
```
/imagine draft: 4-panel manga sequence: 1) DorothyGale engulfed by magical cyclone, 2) falling through dimensional tunnel with mathematical equations and weather patterns visible in tunnel walls, 3) glimpses of evolved Oz visible through tears in reality, 4) DorothyGale landing in field of strange new flowers, disoriented. Manga panel layout with dynamic action lines.
```

5. **First Oz Encounter:**
```
/imagine draft: DorothyGale meeting evolved ScarecrowBrain, now a magical-technological researcher, emotional reunion panel, both characters showing mix of recognition and surprise at how each has changed, magical particles floating in air, yellow brick road visible in background. Character-focused manga composition.
```

## Practical Workflow Tips

1. **Use proper naming conventions** - Always use combined names without spaces (DorothyGale, ScarecrowBrain, TinmanHeart) 
2. **Define characters style-neutrally** - Focus on attributes, not rendering style, in character definitions
3. **Apply styles separately** - Use style descriptors after character definition is established
4. **Document character definitions** - Keep records of successful character prompts for reuse
5. **Restart draft sessions for new sequences** - Character memory only persists within a single session
6. **Create environment establishing shots** to reference in later panels
7. **Save your best images** to use as reference with --sref
8. **Use personalization (-p)** to maintain consistent style preferences
9. **For complex scenes, build elements separately** then combine them in final compositions
10. **Be explicit about style** when switching between manga and photorealistic renderings

## Troubleshooting Common Issues

### Character Consistency Problems:
If Midjourney struggles to maintain character appearance between images:
```
Remember to use "Character [CombinedName]:" prefix when redefining them
```
```
Always use combined names without spaces (DorothyGale not Dorothy Gale)
```
```
Use detailed descriptions of distinct visual elements
```
```
Create reference panels showing character from multiple angles
```

### Name Recognition Issues:
If Midjourney isn't recognizing your character names:
```
Ensure your combined names don't contain spaces (DorothyGale, not Dorothy Gale)
```
```
Avoid names that might be interpreted as descriptive words
```
```
If a name isn't being recognized, try making it more distinctive (DorothyGaleOz instead of DorothyGale)
```
```
Remember character memory only persists within a single draft session
```

### Session Limitations:
When working across multiple sessions:
```
Character definitions only last within the current draft session
```
```
Starting a new draft session requires redefining all characters
```
```
Keep a document with all character definitions for easy reference
```
```
Create reference images for each character to use with --sref parameter
```

### Style Consistency Issues:
If struggling with style consistency:
```
Use --sref with URLs of images in your desired style
```
```
Adjust personalization strength with -p (1-100)
```
```
Be explicit about style in each prompt (manga style, photorealistic style)
```
```
Save successful style examples for reference
```

## Conclusion

Midjourney V7 represents a revolutionary tool for manga creators and storyboard artists, especially when creating a narrative as rich as an adult Dorothy returning to Oz. The combination of Draft Mode for rapid iteration, voice commands for intuitive direction, and character memory features allows you to focus on storytelling rather than prompt engineering.

The key advantages of V7 for this project are:
1. **Character consistency** through proper naming and definition
2. **Style flexibility** that allows you to explore manga and photorealistic renderings
3. **Rapid iteration** through Draft Mode for quick storyboard development
4. **Natural language interaction** through voice commands

Remember that the best manga storyboards balance visual consistency with emotional storytelling. Define your characters clearly with combined names, maintain style consistency through references and personalization, and document your successful approaches for future use.

As you develop your Oz manga storyboard, continuously save your best images to build a reference library that ensures consistency across your entire project. With practice, you'll be able to create a cohesive manga-style narrative that could serve as the foundation for an animated film production.
