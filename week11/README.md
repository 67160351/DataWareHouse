# 2HandToYou — Business Storytelling Dashboard

## Concept
**AI makes a second-hand marketplace more trustworthy and easier to discover.**

The dashboard tells a business story around two AI capabilities:
1. Duplicate / suspicious image detection → improves trust and reduces listing-quality risk.
2. Recommendation → improves product discovery and personalization.

## Files
- `index.html` — interactive storytelling dashboard
- `2HandToYou_Dashboard_Data.xlsx` — cleaned dashboard data + sources + team list
- `source_duplicate_dataset.xlsx` — original duplicate-model dataset used by the project
- `source_recommendation_dataset.xlsx` — original recommendation dataset used by the project
- `README.md` — repository guide

## Data note
The project materials explicitly contain synthetic/mock/test datasets. They are appropriate for model evaluation and dashboard storytelling, but **must not be presented as real 2HandToYou sales or live-user revenue**.

Key supplied project numbers:
- Duplicate training dataset: 20,000 records
- Recommendation dataset: 10,000 records
- Buyer profiles: 1,000
- Buyer behavior events: 5,000
- Recommendation feedback: 5,000
- Duplicate model Phase 1 controlled accuracy: 100%
- Duplicate model Phase 1 macro F1: 100%
- Recommendation feedback CTR: 27.6%
- Recommendation purchase conversion: 6.9%
- Project progress statements: Duplicate 90%, Recommendation 80%

## External market context
Statista describes Thailand ReCommerce as online resale of pre-owned physical products and highlights affordability, sustainability and digital convenience as growth drivers.
Kaidee's public marketplace shows a large and diverse second-hand listing environment across electronics, fashion, home, collectibles and vehicles.

## Suggested repository structure
```
2HandToYou-Dashboard/
├── index.html
├── 2HandToYou_Dashboard_Data.xlsx
├── source_duplicate_dataset.xlsx
├── source_recommendation_dataset.xlsx
└── README.md
```

## Team
Leader: พีรพัฒน์ บุญแสน
Tech: นพรัตน์ โพธิ์สาวัง, นภัสดล หงษาวดี, กิตติทัศน์ แก้วใส
Support: ธมนวรรณ ศรีสวัสดิ์, วิศวรุต พุกพะยา, มงคลชัย ช่วงโชติ
QA: ธนกฤต มะโน, ปัญญาสิริ อาจศัตรู, ธันยา สมบัติ
