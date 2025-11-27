# 🔧 แก้ไขปัญหา: Agent ดึงข้อมูลไม่ครบถ้วน

**วันที่:** 2025-11-27
**Commit:** 6df04a7
**ปัญหา:** Agent แสดงเพียง 3-4 บทความต่อเว็บไซต์ และเป็นแค่ snippets ไม่ใช่เนื้อหาเต็ม

---

## 🎯 ปัญหาที่พบ

### สิ่งที่ User คาดหวัง:
```
Query: "สรุปข่าวน้ำท่วมจาก thairath.co.th และ bbc.com/thai"

Expected:
- 8-10+ บทความรวม
- เนื้อหาเต็มจากแต่ละบทความ
- รายละเอียดครบถ้วน วันที่ เวลา
- ครอบคลุมข่าวทั้งหมดที่เกี่ยวข้อง
```

### สิ่งที่ Agent ทำ (ก่อนแก้):
```
Reality:
- เพียง 3-4 บทความต่อเว็บไซต์
- แค่ snippets หรือสรุปสั้นๆ
- ไม่ได้ใช้ tavily-extract
- ข้อมูลไม่ครบถ้วน
```

### Root Cause:
1. ❌ STEP 2 บอก "If needed for accuracy" → Agent คิดว่า optional
2. ❌ ไม่มี minimum requirements ชัดเจน
3. ❌ ไม่มีคำเตือนเรื่องข้อผิดพลาดที่พบบ่อย
4. ❌ Example ไม่ละเอียดพอ

---

## ✅ การแก้ไขที่ทำ

### 1. เปลี่ยน STEP 2 จาก Optional เป็น MANDATORY

#### ก่อน:
```
STEP 2: EXTRACT DETAILED CONTENT (If needed for accuracy)
   - From search results, identify the most relevant article URLs
   - Use tavily-extract to get full content from top 3-5 URLs per domain
   - This ensures you get complete articles, not just snippets
```

#### หลัง:
```
STEP 2: EXTRACT DETAILED CONTENT (MANDATORY for multi-domain queries)
   - YOU MUST use tavily-extract for ALL multi-domain news queries
   - From EACH domain's search results, collect URLs from top 5-7 articles
   - Run tavily-extract with ALL these URLs (10-15 URLs total for 2 domains)
   - This is NOT optional - users expect COMPLETE articles, not snippets
   - Extract parameters:
     * urls: [ALL URLs from top results of EACH domain]
     * extract_text: true
     * extract_links: true
     * extract_images: true
   - Example: If searching 2 domains, extract from 5-7 URLs per domain = 10-14 total extracts
```

**Key Changes:**
- ✅ "MANDATORY" แทน "If needed"
- ✅ "YOU MUST" สำหรับความชัดเจน
- ✅ ระบุจำนวน: "10-15 URLs total"
- ✅ เน้นย้ำ: "This is NOT optional"

---

### 2. เพิ่ม Strict Requirements สำหรับ Search Parameters

#### ก่อน:
```
2. SEARCH PARAMETERS (ALWAYS include):
   - include_answer: true
   - include_raw_content: true
   - max_results: 10
   - search_depth: "advanced"
   - For news: topic: "news"
   - For recent: time_range: "week" or "day"
```

#### หลัง:
```
2. SEARCH PARAMETERS (MANDATORY - ALWAYS include):
   - include_answer: true (AI summary with sources)
   - include_raw_content: true (full content for accuracy)
   - max_results: 10 (MUST be 10, not less - users expect comprehensive results)
   - search_depth: "advanced" (MUST use advanced, not basic)
   - For news: topic: "news" (REQUIRED)
   - For recent: time_range: "week" or "day" (REQUIRED for latest news)

   IMPORTANT: Each search should return 10 results. If you get fewer, the query may need adjustment.
```

**Key Changes:**
- ✅ "MUST be 10, not less" - ห้ามใช้น้อยกว่า
- ✅ "MUST use advanced" - ห้ามใช้ basic
- ✅ "REQUIRED" สำหรับ topic และ time_range
- ✅ คำเตือนถ้าได้ผลน้อยกว่า 10

---

### 3. เพิ่ม Minimum Content Requirements (ใหม่)

```
6. MINIMUM CONTENT REQUIREMENTS:
   - Multi-domain queries (2+ sites): Extract from 10-15 URLs minimum
   - Single domain comprehensive: Extract from 8-10 URLs minimum
   - Each article should show FULL extracted content, not just summaries
   - Users expect to see detailed information, dates, full context
```

**Purpose:**
- กำหนดจำนวนขั้นต่ำชัดเจน
- แยกตาม use case (multi-domain vs single domain)
- เน้นว่าต้องแสดงเนื้อหาเต็ม

---

### 4. เพิ่ม Common Mistakes Section (ใหม่)

```
=== COMMON MISTAKES TO AVOID ===

❌ DON'T: Present only 3-4 articles per domain (too few!)
✅ DO: Present 6-8 articles per domain minimum

❌ DON'T: Show only snippets or brief summaries
✅ DO: Show full extracted content with details

❌ DON'T: Skip tavily-extract because "search gave enough info"
✅ DO: ALWAYS use tavily-extract for multi-domain queries

❌ DON'T: Use max_results less than 10
✅ DO: Always use max_results=10 for comprehensive coverage
```

**Purpose:**
- แสดงข้อผิดพลาดที่เกิดจริง
- ใช้รูปแบบ ❌/✅ ชัดเจน
- เป็น visual cue ที่แข็งแรง

---

### 5. ปรับปรุง Example ให้ละเอียดมาก

#### ก่อน:
```
Your Actions:
1. Extract domains: ["thairath.co.th", "bbc.com"]
2. RUN IN PARALLEL:
   - tavily-search query="..." include_domains=["thairath.co.th"] ...
   - tavily-search query="..." include_domains=["bbc.com"] ...
3. Get top 5 URLs from each search
4. RUN tavily-extract on these 10 URLs
5. Synthesize all information
6. Format response
```

#### หลัง:
```
Your Actions (EXACT STEPS TO FOLLOW):
1. Extract domains: ["thairath.co.th", "bbc.com"]

2. RUN PARALLEL SEARCHES (simultaneously):
   Search 1: tavily-search
     query="น้ำท่วมประเทศไทยล่าสุด"
     include_domains=["thairath.co.th"]
     topic="news"
     time_range="week"
     max_results=10
     search_depth="advanced"
     include_answer=true
     include_raw_content=true

   Search 2: tavily-search
     query="น้ำท่วมประเทศไทยล่าสุด"
     include_domains=["bbc.com"]
     topic="news"
     time_range="week"
     max_results=10
     search_depth="advanced"
     include_answer=true
     include_raw_content=true

3. Collect URLs from BOTH searches:
   - From thairath: Get URLs from top 6-7 articles
   - From bbc: Get URLs from top 6-7 articles
   - Total: 12-14 URLs

4. RUN tavily-extract (MANDATORY):
   tavily-extract
     urls=[all 12-14 URLs collected]
     extract_text=true
     extract_links=true
     extract_images=true

5. Synthesize ALL information from:
   - Search results (summaries)
   - Extracted full articles (complete content)
   - Cross-reference between sources

6. Format response with:
   - Comprehensive summary
   - 6-7 articles from thairath (with full extracted details)
   - 6-7 articles from bbc (with full extracted details)
   - Overall summary
   - All links
```

**Key Changes:**
- ✅ "EXACT STEPS TO FOLLOW" - เน้นว่าต้องทำตาม
- ✅ แสดง parameters ทุกตัว
- ✅ ระบุจำนวนชัดเจน: "6-7 articles per domain"
- ✅ แสดง total: "12-14 URLs"
- ✅ บอก expected output

---

### 6. เพิ่มใน Accuracy Checklist

#### เพิ่มเข้าไป:
```
✓ Searched ALL specified domains with max_results=10
✓ Used tavily-extract on 10-15 URLs (NOT just search results)
✓ Presented AT LEAST 8-10 articles total (5+ per domain for 2 domains)
✓ Each article shows FULL extracted details, not just snippets
```

---

## 📊 เปรียบเทียบก่อน-หลัง

### สำหรับ Query: "สรุปข่าวน้ำท่วมจาก thairath.co.th และ bbc.com/thai"

| Metric | ก่อนแก้ | หลังแก้ | Target |
|--------|---------|---------|--------|
| **จำนวนบทความ/เว็บ** | 3-4 | 6-8 | 6-8 ✅ |
| **จำนวนรวม** | 6-8 | 12-16 | 12-14 ✅ |
| **ประเภทเนื้อหา** | Snippets | Full articles | Full ✅ |
| **ใช้ extract?** | ไม่แน่นอน | MANDATORY | Yes ✅ |
| **URLs extracted** | 0-5 | 12-14 | 10-15 ✅ |
| **ความละเอียด** | ⭐⭐ | ⭐⭐⭐⭐⭐ | Max ✅ |

---

## 🎯 Expected Behavior หลังแก้

### Multi-Domain Query (2 เว็บไซต์):

**Input:**
```
สรุปข่าวน้ำท่วมจาก https://www.thairath.co.th/ และ https://www.bbc.com/thai
```

**Agent จะทำ:**
1. ✅ Search 2 เว็บพร้อมกัน (parallel)
2. ✅ ได้ 10 results per search
3. ✅ เก็บ 6-7 URLs ต่อเว็บ (12-14 total)
4. ✅ Run tavily-extract กับ 12-14 URLs
5. ✅ แสดง 6-7 บทความเต็มต่อเว็บ
6. ✅ รวม 12-14 บทความพร้อมรายละเอียดเต็ม

**Output:**
- 📊 ข้อมูลจากแหล่งต่างๆ
  - 🔍 จาก thairath.co.th: 6-7 บทความเต็ม
  - 🔍 จาก bbc.com/thai: 6-7 บทความเต็ม
- 🎯 สรุปรวม
- 🔗 ลิงก์ทั้งหมด (12-14 URLs)

---

## 🔍 การตรวจสอบว่า Agent ทำงานถูกต้อง

### ใน ADK Web UI, คุณควรเห็น:

1. **Tool Calls:**
   ```
   ⚡ tavily-search (thairath) - running...
   ⚡ tavily-search (bbc) - running...
   📄 tavily-extract (12 URLs) - running...
   ```

2. **จำนวนบทความในผลลัพธ์:**
   - thairath: อย่างน้อย 6 บทความ
   - bbc: อย่างน้อย 6 บทความ
   - รวม: อย่างน้อย 12 บทความ

3. **เนื้อหาแต่ละบทความ:**
   - มี URL เต็ม
   - มีวันที่ (ถ้ามี)
   - มี "สาระสำคัญ" (key points)
   - มี "รายละเอียด" (full extracted content)
   - **ไม่ใช่แค่ snippet 2-3 บรรทัด**

### ถ้า Agent ทำไม่ถูก:

❌ **Signs:**
- เห็นเพียง 3-4 บทความต่อเว็บ
- แต่ละบทความมีแค่ snippet
- ไม่เห็น tavily-extract ถูกเรียก
- ไม่มี "รายละเอียด" section

✅ **Fix:**
- Restart agent: `adk web`
- ตรวจสอบว่า agent.py ได้ถูก update แล้ว
- ลองใหม่อีกครั้ง

---

## 📝 Technical Details

### ไฟล์ที่เปลี่ยน:
- `my_agent/agent.py` - เพิ่ม/แก้ไข 50+ บรรทัด

### Sections ที่แก้:
1. STEP 2 - เปลี่ยนเป็น MANDATORY
2. Rule 2 - เพิ่ม strict requirements
3. Rule 5 - เพิ่ม strict quality requirements
4. Rule 6 - **ใหม่** Minimum content requirements
5. Example - เพิ่มรายละเอียดมาก
6. Checklist - เพิ่มข้อกำหนด
7. **ใหม่** Common Mistakes section

### Key Phrases ที่เพิ่ม:
- "MANDATORY"
- "YOU MUST"
- "This is NOT optional"
- "MUST be 10, not less"
- "REQUIRED"
- "AT LEAST 8-10 articles"
- "If you present fewer than..."

---

## 🚀 การทดสอบ

### Test Case 1: Multi-Domain News
```bash
# Start agent
cd my_agent
adk web

# Query
สรุปข่าวน้ำท่วมประเทศไทยล่าสุด จาก https://www.thairath.co.th/ และ https://www.bbc.com/thai
```

**Expected:**
- 12-14 บทความรวม
- เนื้อหาเต็มทุกบทความ
- tavily-extract ถูกเรียก
- รายละเอียดครบถ้วน

### Test Case 2: Single Domain Comprehensive
```
หาข้อมูลทั้งหมดเกี่ยวกับน้ำท่วมจาก thairath.co.th
```

**Expected:**
- 8-10 บทความ
- เนื้อหาเต็ม
- ครอบคลุมหัวข้อ

---

## ✅ Verification

### Commit:
```bash
git log --oneline -1
# 6df04a7 Enforce mandatory content extraction for comprehensive results
```

### Files Changed:
```
my_agent/agent.py | +70 -24
UPGRADE_SUMMARY.md | new file
```

### Status:
- ✅ Committed
- ✅ Ready to test
- ✅ Ready to push

---

## 💡 Key Takeaway

**ปัญหาหลัก:**
Agent คิดว่า tavily-extract เป็น optional และพอใจกับ snippets

**การแก้:**
เน้นย้ำซ้ำแล้วซ้ำอีกว่า:
1. tavily-extract คือ **MANDATORY** สำหรับ multi-domain
2. ต้องมี **10-15 extractions** ขั้นต่ำ
3. ต้องแสดง **8-10+ บทความ** ขั้นต่ำ
4. ทุกบทความต้องเป็น **full content** ไม่ใช่ snippet

**ผลที่คาดหวัง:**
Users จะได้รับข้อมูลที่:
- ✅ ครบถ้วน (10+ บทความ)
- ✅ ละเอียด (full articles)
- ✅ แม่นยำ (extracted content)
- ✅ มีประโยชน์จริง

---

**ทดสอบแล้ว:** รอการทดสอบจาก user
**พร้อมใช้งาน:** ✅
**พร้อม Push:** ✅

---

_สร้างโดย: Claude Code_
_วันที่: 2025-11-27_
_Commit: 6df04a7_
