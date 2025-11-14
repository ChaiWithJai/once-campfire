# Claude Configuration for Campfire

This directory contains Claude Code skills and configuration for the Campfire project.

## Available Skills

### 📚 Technical Onboarding Coach

**File:** `skills/technical-onboarding-coach.md`

**Purpose:** Generates comprehensive technical onboarding documentation that transforms complex engineering concepts into accessible, progressive learning materials.

**Use this skill when you need to:**
- Onboard new team members with varying technical backgrounds
- Create learning materials for engineers learning a new codebase
- Develop language skills (B1→C2 English) alongside technical skills
- Build communication scripts and presentation templates
- Establish assessment criteria for technical competency

**How to use:**

```bash
# In Claude Code, invoke the skill:
/skill technical-onboarding-coach
```

Or using the Skill tool programmatically:
```
Use the Skill tool with command: "technical-onboarding-coach"
```

**What it generates:**
1. **Onboarding Documentation** - Comprehensive guide tailored to this codebase
2. **12 Weekly Learning Modules** - Progressive lessons with hands-on exercises
3. **Language Development Guide** - Technical English progression (B1→C2)
4. **Assessment Rubrics** - Measurable success criteria
5. **Communication Scripts** - Templates for standups, presentations, code reviews

**Output location:**
All materials are generated in `/docs/onboarding/` with organized subdirectories.

**Customization:**
The skill analyzes the actual codebase and creates materials specific to:
- Real file paths and line numbers
- Actual features and user stories
- The technology stack in use
- Specific architectural patterns

**Target audience:**
- Engineers with advanced education but no real work experience
- Team members learning a new codebase
- Non-native English speakers developing professional technical communication
- Anyone needing structured, progressive learning materials

---

## Directory Structure

```
.claude/
├── README.md                           # This file
├── skills/                             # Claude Code skills
│   └── technical-onboarding-coach.md   # Onboarding documentation generator
└── [future skills and configuration]
```

---

## Adding New Skills

To add a new skill:

1. Create a markdown file in `.claude/skills/`
2. Add frontmatter with metadata:
   ```yaml
   ---
   skill: skill-name
   description: Brief description
   tags: [tag1, tag2]
   ---
   ```
3. Write the skill prompt and instructions
4. Test by invoking: `/skill skill-name`

---

## About Claude Code Skills

Skills are specialized prompts that help Claude perform complex, domain-specific tasks. They:
- Provide detailed context and instructions
- Follow consistent output formats
- Can be reused across projects
- Help maintain quality and consistency

For more information, see: https://docs.claude.com/docs/claude-code

---

**Project:** Campfire - Real-time chat application
**Maintained by:** Development Team
