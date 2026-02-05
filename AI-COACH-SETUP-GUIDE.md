# AI Coach Funnel — Complete Setup Guide

## Overview

This guide covers the complete setup of your AI Coach qualification funnel:

```
┌─────────────────┐     ┌─────────────┐     ┌──────────────────┐
│  Landing Page   │ ──► │   Survey    │ ──► │  Thank You Page  │
│  (tripwire)     │     │  (10 questions)   │  (qualified/nurture)
└─────────────────┘     └─────────────┘     └──────────────────┘
                              │
                              ▼
                    ┌─────────────────┐
                    │   GHL Webhook   │
                    │   (automation)  │
                    └─────────────────┘
                              │
                              ▼
                    ┌─────────────────┐
                    │  Contact Created │
                    │  + Workflow      │
                    └─────────────────┘
```

---

## Files Included

| File | Purpose |
|------|---------|
| `ai-coach-tripwire-RU.html` | Main landing page |
| `ai-coach-survey-RU.html` | 10-step qualification survey |
| `ai-coach-thank-you-qualified-RU.html` | Success page for qualified leads |
| `ai-coach-thank-you-nurture-RU.html` | Nurture page for cold leads |
| `ai-coach-survey-questions-RU.md` | Survey strategy documentation |

---

## Step 1: GHL Webhook Setup

### 1.1 Create the Workflow

1. In GHL, go to **Automation → Workflows**
2. Click **Create Workflow**
3. Select **Start from Scratch**
4. Name it: `AI Coach Survey Submissions`

### 1.2 Add Webhook Trigger

1. Click **Add New Trigger**
2. Select **Webhook**
3. Copy the webhook URL — it looks like:
   ```
   https://services.leadconnectorhq.com/hooks/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   ```
4. Save this URL — you'll need it in Step 2

### 1.3 Build the Workflow

Add these actions in order:

```
[Webhook Trigger]
       │
       ▼
[Create/Update Contact]
   • First Name: {{first_name}}
   • Phone: {{phone}}
   • Email: {{email}}
   • Tags: ai-coach-survey
       │
       ▼
[Update Contact Custom Fields]
   • Lead Score: {{lead_score}}
   • Lead Status: {{lead_status}}
   • Survey Goal: {{q5_answer_text}}
   • Survey Areas: {{q4_answer_text}}
   • Survey Readiness: {{q9_answer_value}}
       │
       ▼
[Add Note to Contact]
   • Note: {{survey_summary}}
       │
       ▼
[IF/ELSE Branch]
   • Condition: {{lead_status}} equals "qualified"
       │
       ├── YES ─────────────────────────────┐
       │                                     │
       ▼                                     ▼
[Add Tag: Hot Lead]              [Add Tag: Nurture Lead]
       │                                     │
       ▼                                     ▼
[Send Internal Notification]     [Add to Email Sequence:
   • To: Sales Team                 "AI Coach Nurture"]
   • Subject: 🔥 Hot Lead: {{first_name}}
   • Body: {{survey_summary}}
       │
       ▼
[Add to Pipeline]
   • Pipeline: Sales Pipeline
   • Stage: New Lead
       │
       ▼
[Wait: 5 minutes]
       │
       ▼
[Send SMS/WhatsApp]
   • To: {{phone}}
   • Message: "Привет, {{first_name}}!
     Спасибо за заполнение анкеты.
     Наш эксперт свяжется с вами в течение 24 часов.

     А пока посмотрите это видео: [link]"
```

### 1.4 Create Custom Fields in GHL

Go to **Settings → Custom Fields → Contacts** and create:

| Field Name | Field Key | Type |
|------------|-----------|------|
| Lead Score | `lead_score` | Number |
| Lead Status | `lead_status` | Single Line Text |
| Survey Goal | `survey_goal` | Multi Line Text |
| Survey Areas | `survey_areas` | Single Line Text |
| Survey Readiness | `survey_readiness` | Number |
| Survey Reason | `survey_reason` | Single Line Text |
| Preferred Call Time | `preferred_call_time` | Single Line Text |

---

## Step 2: Configure the Survey HTML

### 2.1 Update Webhook URL

Open `ai-coach-survey-RU.html` and find this section (around line 1112):

```javascript
const GHL_CONFIG = {
    // REPLACE THIS WITH YOUR WEBHOOK URL FROM STEP 1.2
    webhookUrl: 'https://services.leadconnectorhq.com/hooks/YOUR_WEBHOOK_ID_HERE',

    // Update these if using GHL funnel pages instead of HTML files
    qualifiedRedirect: 'ai-coach-thank-you-qualified-RU.html',
    nurtureRedirect: 'ai-coach-thank-you-nurture-RU.html',

    // Adjust qualification threshold (0-70 scale)
    qualifyThreshold: 40
};
```

### 2.2 Update Thank You Page URLs

If hosting thank you pages on GHL funnels, update the redirect URLs:

```javascript
qualifiedRedirect: 'https://your-domain.com/thank-you-qualified',
nurtureRedirect: 'https://your-domain.com/thank-you-nurture',
```

---

## Step 3: Upload to GHL

### Option A: Custom Code in Funnel

1. Go to **Sites → Funnels**
2. Create new funnel or open existing
3. Add a new step for each page
4. In the page editor, add **Custom Code** element
5. Paste the entire HTML file content
6. Save and publish

### Option B: Hosted HTML Files

1. Upload HTML files to your web hosting
2. Update all internal links to use full URLs
3. Link from GHL to your hosted pages

### Option C: GHL Websites

1. Go to **Sites → Websites**
2. Create pages and add Custom Code blocks
3. Paste HTML content into each block

---

## Step 4: Update Landing Page CTA

In `ai-coach-tripwire-RU.html`, find the CTA buttons and update the href:

```html
<!-- Find these buttons and update href -->
<a href="ai-coach-survey-RU.html" class="btn-main">
    Пройти диагностику
</a>
```

Change to your GHL funnel URL:
```html
<a href="https://your-domain.com/ai-coach-survey" class="btn-main">
    Пройти диагностику
</a>
```

---

## Step 5: Update Thank You Pages

### Qualified Page (`ai-coach-thank-you-qualified-RU.html`)

Find and update the calendar booking link (around line 155):

```javascript
// Replace with your Calendly or GHL calendar link
document.getElementById('calendarBtn').href = 'https://calendly.com/your-link';
```

Or use GHL calendar:
```javascript
document.getElementById('calendarBtn').href = 'https://api.leadconnectorhq.com/widget/booking/YOUR_CALENDAR_ID';
```

### Nurture Page (`ai-coach-thank-you-nurture-RU.html`)

Update resource links to your actual lead magnets:

```html
<a href="YOUR_PDF_LINK" class="resource-item">
    <div class="resource-icon">📚</div>
    <div class="resource-content">
        <h3>Гайд: 10 способов использовать ChatGPT</h3>
        ...
    </div>
</a>
```

Update social links:
```html
<a href="https://t.me/your_channel" class="social-link tg">
```

---

## Step 6: Testing

### 6.1 Test the Survey Flow

1. Open survey page in browser
2. Open browser DevTools (F12) → Console tab
3. Fill out survey with test data
4. On submit, check console for `Sending to GHL:` log
5. Verify redirect to correct thank you page

### 6.2 Test GHL Webhook

1. In GHL Workflow, click **Test Workflow**
2. Or submit real test data through survey
3. Check **Contacts** for new entry
4. Verify all custom fields populated
5. Check **Conversations** for automated messages
6. Verify internal notification received

### 6.3 Test Lead Scoring

Test different scenarios:

| Scenario | Expected Score | Expected Result |
|----------|----------------|-----------------|
| New person, curious only, 1-3 readiness | ~0 | Nurture |
| Tried ChatGPT, 6+ months, readiness 8 | 50+ | Qualified |
| Paid courses, 10h+/week wasted, readiness 9 | 60+ | Qualified |

---

## Step 7: Go Live Checklist

- [ ] Webhook URL updated in survey HTML
- [ ] Thank you page URLs updated
- [ ] Calendar booking link updated
- [ ] Resource/lead magnet links updated
- [ ] Social links updated
- [ ] GHL workflow tested and activated
- [ ] Custom fields created in GHL
- [ ] Pipeline/stages created in GHL
- [ ] Internal notification recipients set
- [ ] Nurture email sequence created
- [ ] SMS/WhatsApp templates approved
- [ ] All pages mobile tested
- [ ] Form submitted with test data
- [ ] Contact created in GHL verified
- [ ] Workflow actions executed verified

---

## Data Reference

### Webhook Payload Fields

All fields sent to GHL webhook:

```json
{
  // Contact Info
  "first_name": "Алексей",
  "phone": "+7 777 123 4567",
  "email": "alex@mail.ru",

  // Scoring
  "lead_score": 55,
  "lead_status": "qualified",

  // Source
  "source": "AI Coach Survey",
  "form_name": "ai-coach-qualification-survey",

  // Q1: Reason
  "q1_question": "Что привело вас сюда сегодня?",
  "q1_answer_value": "need_mentor",
  "q1_answer_text": "Нужен наставник, но нет бюджета на дорогого коуча",

  // Q2: Time Trying
  "q2_question": "Как давно вы пытаетесь решить эту задачу?",
  "q2_answer_value": "months",
  "q2_answer_text": "Несколько месяцев",

  // Q3: Wasted Hours
  "q3_question": "Сколько времени в неделю тратите на поиск информации...",
  "q3_answer_value": "5_10h",
  "q3_answer_text": "5-10 часов",

  // Q4: Areas (multi-select)
  "q4_question": "В какой сфере нужна помощь больше всего?",
  "q4_answer_value": "career, money, learning",
  "q4_answer_text": "💼 Карьера...; 💰 Финансы...; 📚 Обучение...",

  // Q5: Goal (free text)
  "q5_question": "Главная цель на ближайшие 6 месяцев",
  "q5_answer_text": "Хочу освоить графический дизайн и начать фриланс",

  // Q6: Mentor Benefits (multi-select)
  "q6_question": "Что бы изменилось с персональным ментором 24/7?",
  "q6_answer_value": "clear_plan, faster",
  "q6_answer_text": "Получил бы чёткий план; Двигался бы быстрее",

  // Q7: Previous Attempts (multi-select)
  "q7_question": "Что уже пробовали для достижения целей?",
  "q7_answer_value": "paid_courses, chatgpt_failed",
  "q7_answer_text": "Покупал платные курсы; Пробовал ChatGPT, но не получил результата",

  // Q8: Time Commitment
  "q8_question": "Сколько времени готовы уделять работе с AI-ментором?",
  "q8_answer_value": "30min",
  "q8_answer_text": "30 минут в день",

  // Q9: Readiness Scale
  "q9_question": "По шкале от 1 до 10, насколько готовы изменить жизнь...",
  "q9_answer_value": "8",
  "q9_answer_text": "8 из 10",

  // Q10: Call Time
  "q10_question": "Удобное время для звонка",
  "q10_answer_value": "day",
  "q10_answer_text": "День (12:00 - 18:00)",

  // Summary (formatted for notes)
  "survey_summary": "📋 АНКЕТА AI-МЕНТОР\n\n👤 Алексей\n📱 +7 777...\n..."
}
```

### Lead Scoring Logic

| Criteria | Points |
|----------|--------|
| Trying 6+ months (q2: months, year_plus, years_no_result) | +10 |
| Wastes 5+ hours/week (q3: 5_10h, 10h_plus) | +10 |
| Tried ChatGPT but failed (q7 includes chatgpt_failed) | +15 |
| Bought paid courses (q7 includes paid_courses) | +10 |
| Ready to commit 30+ min/day (q8: 30min, 1h, whatever_needed) | +10 |
| Readiness 7+ out of 10 (q9 >= 7) | +15 |
| **Maximum possible score** | **70** |
| **Qualification threshold** | **40** |

---

## Troubleshooting

### Webhook Not Receiving Data

1. Check browser console for errors
2. Verify webhook URL is correct (no typos)
3. Test webhook with Postman/curl:
   ```bash
   curl -X POST https://services.leadconnectorhq.com/hooks/YOUR_ID \
     -H "Content-Type: application/json" \
     -d '{"first_name":"Test","phone":"+77771234567"}'
   ```
4. Check GHL workflow is **Published** (not draft)

### Contact Not Created

1. Verify workflow has "Create/Update Contact" action
2. Check field mappings use correct `{{field_name}}` syntax
3. Ensure phone number format is valid

### Redirect Not Working

1. Check `qualifiedRedirect` and `nurtureRedirect` URLs are accessible
2. If using relative paths, ensure files are in same directory
3. Check for CORS errors in console

### Scoring Seems Wrong

1. Check browser console for `Lead Score:` log
2. Verify answers match scoring criteria
3. Adjust `qualifyThreshold` if needed

---

## Support

- GHL Documentation: https://help.gohighlevel.com/
- GHL Community: https://www.facebook.com/groups/gikigroup

---

*Last updated: February 2026*
