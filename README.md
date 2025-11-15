# 📊 Demon Slayer Analytics Dashboard | Power BI Portfolio Project

A comprehensive **Business Intelligence solution** demonstrating enterprise-level analytics through entertainment data. This project showcases advanced Power BI skills including data modeling, DAX calculations, and interactive dashboard design.

[📺 View Live Demo](#) | [📄 Documentation](#) | [💼 LinkedIn Post](#)

---

## 🎯 Project Overview

This project transforms anime story data into a professional **3-dashboard analytics suite**, applying real-world business intelligence methodologies to entertainment metrics. Built to demonstrate proficiency in Power BI for data analyst and BI developer roles.

### **Business Questions Answered:**
- Which story arcs drive the highest engagement and viewership?
- How do character abilities correlate with popularity metrics?
- What regional markets show the strongest revenue performance?
- How does fan sentiment correlate with sales trends?
- What factors contribute to mission success rates?

---

## 📈 Dashboard Suite

### **1. Story Performance Dashboard**
Comprehensive arc-level analytics tracking viewership, ratings, and engagement metrics.

**Key Features:**
- 📊 Total Views, Average Rating, Total Mentions, Arc Performance Index KPIs
- 📉 Viewership trend analysis across story arcs
- 🎯 Arc rating comparison with visual benchmarking
- 🗡️ Mission success rate breakdown by difficulty level
- 🕸️ Arc strength balance radar chart
- 📋 Detailed episode performance table with drill-through capability

**Insights Delivered:**
- Entertainment District arc achieved highest rating (9.17)
- 65M total views across all arcs with 8.92 average rating
- Strong correlation between arc complexity and viewer engagement

![Story Performance Dashboard](./screenshots/dashboard1.png)

---

### **2. Character Insights Dashboard**
Multi-dimensional character analysis combining performance metrics with ability assessments.

**Key Features:**
- 💪 Popularity Index (15.41), Average Power Level (73.03), Win Rate (100%)
- 📍 Engagement strength scatter plot (Win Rate vs. Mentions)
- 🕸️ 10-point radar chart for character ability comparison
- 📊 Horizontal bar chart for popularity rankings
- 📉 Mission performance by character (vertical bar chart)
- 📋 Detailed character performance table with role and breathing style filters

**Advanced Analytics:**
- Power Level calculations using weighted formulas
- Popularity indexing based on fan mentions and screen time
- Multi-attribute comparison across Speed, Strength, Power, Technique

![Character Insights Dashboard](./screenshots/dashboard2.png)

---

### **3. Fan & Market Universe Dashboard**
Revenue and engagement analytics with regional market breakdown.

**Key Features:**
- 💰 Franchise Power Score (1.96), Average Sentiment (76%), Total Sales ($4.3M)
- 🌍 Regional performance table with sales and units breakdown
- 📈 Dual-axis combo chart: Sales vs. Engagement trends
- 🎯 Scatter plot: Engagement vs. Popularity correlation
- 📦 Product category sales by region (grouped bar chart)

**Business Metrics:**
- $4.3M in merchandise revenue across 5 regions
- 76% positive sentiment with 8.2% period-over-period growth
- Strongest markets: Japan, USA, Europe

![Fan & Market Dashboard](./screenshots/dashboard3.png)

---

## 🛠️ Technical Implementation

### **Data Model Architecture**
```
Star Schema Design:
├── Fact_Episodes (Grain: Episode level)
├── Fact_Sales (Grain: Transaction level)
├── Dim_Characters (22 unique characters)
├── Dim_Arcs (12 story arcs)
├── Dim_Regions (5 regions)
└── Dim_Date (Calendar table)
```

**Relationships:**
- One-to-Many: Dim_Arcs → Fact_Episodes
- One-to-Many: Dim_Characters → Fact_Episodes
- One-to-Many: Dim_Date → Fact_Sales
- Many-to-Many: Characters ↔ Arcs (Bridge table)

---

### **DAX Measures Library**

#### **KPI Calculations**
```DAX
// Total Views with formatting
Total Views = 
VAR TotalViews = SUM(Fact_Episodes[Views])
RETURN
    IF(TotalViews >= 1000000, 
       FORMAT(TotalViews / 1000000, "0.0") & "M",
       FORMAT(TotalViews / 1000, "0") & "K")

// Average Rating
Average Rating = 
AVERAGE(Fact_Episodes[Rating])

// Arc Performance Index (Weighted Score)
Arc Performance Index = 
VAR AvgViews = AVERAGE(Fact_Episodes[Views])
VAR AvgRating = AVERAGE(Fact_Episodes[Rating])
VAR MaxViews = MAXX(ALL(Fact_Episodes), [Views])
VAR MaxRating = 10
RETURN
    ((AvgViews / MaxViews) * 0.6 + (AvgRating / MaxRating) * 0.4) * 100
```

#### **Time Intelligence**
```DAX
// Previous Period Comparison
Previous Period Views = 
CALCULATE(
    [Total Views],
    DATEADD(Dim_Date[Date], -1, MONTH)
)

// Period-over-Period Growth
Views Growth % = 
VAR CurrentViews = [Total Views]
VAR PreviousViews = [Previous Period Views]
RETURN
    DIVIDE(CurrentViews - PreviousViews, PreviousViews, 0)
```

#### **Character Analytics**
```DAX
// Popularity Index (Composite Score)
Popularity Index = 
VAR Mentions = SUM(Fact_Characters[Fan_Mentions])
VAR ScreenTime = SUM(Fact_Characters[Screen_Time_Minutes])
VAR Merch = SUM(Fact_Sales[Units_Sold])
RETURN
    (Mentions * 0.4 + ScreenTime * 0.3 + Merch * 0.3) / 1000

// Power Level (Weighted Average)
Average Power Level = 
AVERAGEX(
    Dim_Characters,
    [Strength] * 0.25 + [Speed] * 0.25 + [Technique] * 0.25 + [Power] * 0.25
)

// Win Rate
Win Rate = 
DIVIDE(
    CALCULATE(COUNT(Fact_Missions[Mission_ID]), Fact_Missions[Success] = TRUE),
    COUNT(Fact_Missions[Mission_ID]),
    0
)
```

#### **Market Analytics**
```DAX
// Total Sales Revenue
Total Sales = 
SUMX(
    Fact_Sales,
    Fact_Sales[Units_Sold] * Fact_Sales[Price_Per_Unit]
)

// Average Sentiment Score
Average Sentiment = 
AVERAGE(Fact_Engagement[Sentiment_Score]) * 100

// Franchise Power Score (Engagement Velocity)
Franchise Power Score = 
VAR RecentEngagement = 
    CALCULATE(
        SUM(Fact_Engagement[Total_Interactions]),
        DATESINPERIOD(Dim_Date[Date], MAX(Dim_Date[Date]), -30, DAY)
    )
VAR HistoricalAvg = 
    CALCULATE(
        AVERAGE(Fact_Engagement[Total_Interactions]),
        ALL(Dim_Date)
    )
RETURN
    DIVIDE(RecentEngagement, HistoricalAvg, 0)
```

#### **Advanced Filtering**
```DAX
// Dynamic Arc Filter
Selected Arc = 
IF(
    ISFILTERED(Dim_Arcs[Arc_Name]),
    SELECTEDVALUE(Dim_Arcs[Arc_Name]),
    "All Arcs"
)

// Top N Characters by Metric
Top 10 Characters = 
VAR CurrentRank = RANKX(ALL(Dim_Characters), [Popularity Index], , DESC)
RETURN
    IF(CurrentRank <= 10, 1, 0)
```

---

### **Visualizations & Custom Features**

#### **Custom Visuals Used:**
- ✅ Radar Chart (Character ability comparison)
- ✅ Combo Chart (Dual-axis sales vs engagement)
- ✅ Scatter Plot with bubble sizing
- ✅ Conditional formatting on tables
- ✅ Custom tooltips with additional context

#### **Interactivity Features:**
- 🔗 Cross-filtering across all visuals
- 🎯 Drill-through from summary to detail views
- 🎨 Slicers for Arc, Character Role, Breathing Style, Region
- 📊 Bookmarks for different view states
- 🔄 Reset filters button on each dashboard

#### **Design Principles:**
- **Color Scheme:** Dark theme with Demon Slayer-inspired accents
  - Background: `#0a0a14` to `#1a1a2e`
  - Primary: `#60a5fa` (Blue)
  - Secondary: `#34d399` (Green), `#f87171` (Red)
  - Accent: `#a78bfa` (Purple)
- **Typography:** Segoe UI for clarity and professionalism
- **Layout:** 16:9 aspect ratio (1280×720px) optimized for presentations
- **Accessibility:** High contrast ratios, clear labeling, colorblind-friendly palette

---

## 📂 Project Structure
```
Demon-Slayer-Analytics/
│
├── data/
│   ├── raw/
│   │   ├── episodes_data.csv
│   │   ├── characters_data.csv
│   │   ├── sales_data.csv
│   │   └── engagement_data.csv
│   └── processed/
│       └── cleaned_data.xlsx
│
├── power-bi/
│   ├── Demon_Slayer_Analytics.pbix
│   └── Demon_Slayer_Analytics.pdf (Dashboard exports)
│
├── screenshots/
│   ├── dashboard1.png
│   ├── dashboard2.png
│   └── dashboard3.png
│
├── documentation/
│   ├── data_dictionary.md
│   ├── dax_formulas.md
│   └── design_decisions.md
│
├── scripts/
│   └── data_preparation.py (Optional data cleaning)
│
├── README.md
└── LICENSE
```

---

## 🚀 Getting Started

### **Prerequisites**
- Microsoft Power BI Desktop (Latest version)
- Basic understanding of DAX and data modeling
- Sample dataset (provided in `/data` folder)

### **Installation Steps**

1. **Clone the repository**
```bash
   git clone https://github.com/yourusername/demon-slayer-analytics.git
   cd demon-slayer-analytics
```

2. **Open the Power BI file**
```
   Navigate to /power-bi/Demon_Slayer_Analytics.pbix
   Double-click to open in Power BI Desktop
```

3. **Refresh data connections**
   - Go to Home → Refresh
   - Verify data source paths if needed
   - Update file paths in Power Query if using local files

4. **Explore the dashboards**
   - Navigate through the 3 dashboard pages
   - Test interactive filters and slicers
   - Hover over visuals for detailed tooltips

---

## 📊 Data Sources

### **Primary Datasets:**
- **Episodes Data:** 120 rows, 8 columns (Arc, Episode, Views, Rating, etc.)
- **Characters Data:** 22 rows, 12 columns (Name, Role, Abilities, Stats)
- **Sales Data:** 1,500 rows, 6 columns (Region, Product, Sales, Date)
- **Engagement Data:** 500 rows, 5 columns (Platform, Mentions, Sentiment)

### **Data Collection Methodology:**
- Simulated dataset based on publicly available information
- Normalized and cleaned using Python (pandas)
- Structured for optimal Power BI performance

> **Note:** This is a portfolio project using simulated data for demonstration purposes.

---

## 💡 Key Learnings & Challenges

### **Technical Challenges Solved:**
1. **Many-to-Many Relationships:** Created bridge table for Characters ↔ Arcs
2. **Performance Optimization:** Reduced file size by 40% through query folding
3. **Complex DAX:** Implemented context transition for accurate calculations
4. **Visual Density:** Balanced information richness with clean design

### **Skills Developed:**
- ✅ Advanced DAX (CALCULATE, FILTER, EARLIER, RANKX)
- ✅ Star schema design for optimal query performance
- ✅ Custom color theming and consistent UI/UX
- ✅ Business storytelling through data visualization
- ✅ Cross-functional dashboard design

---

## 🎓 Use Cases & Applications

This project demonstrates skills applicable to:

**Industries:**
- 📺 Media & Entertainment Analytics
- 🛍️ E-commerce & Retail
- 📱 Social Media & Community Management
- 🎮 Gaming Analytics
- 📊 Market Research

**Job Roles:**
- Data Analyst
- Business Intelligence Developer
- Power BI Developer
- Data Visualization Specialist
- Analytics Consultant

---

## 🔮 Future Enhancements

- [ ] Add predictive analytics using Python integration
- [ ] Implement real-time data refresh via API
- [ ] Create mobile-optimized dashboard layouts
- [ ] Add drill-through pages for deeper analysis
- [ ] Integrate Power Automate for automated reporting
- [ ] Build companion Power Apps for data entry
- [ ] Add R visuals for advanced statistical analysis
- [ ] Create paginated reports for executive distribution

---

## 📸 Screenshots

### Dashboard 1: Story Performance
![Dashboard 1](./screenshots/dashboard1.png)

### Dashboard 2: Character Insights
![Dashboard 2](./screenshots/dashboard2.png)

### Dashboard 3: Market Universe
![Dashboard 3](./screenshots/dashboard3.png)

---

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👤 Author

**Your Name**
- LinkedIn: [your-linkedin](https://linkedin.com/in/yourprofile)
- Portfolio: [your-portfolio](https://yourportfolio.com)
- Email: your.email@example.com

---

## 🙏 Acknowledgments

- Power BI community for inspiration and best practices
- Microsoft documentation for DAX reference
- Demon Slayer fandom for data insights
- DataViz designers for UI/UX inspiration

---

## 📌 Keywords

`Power BI` `DAX` `Data Analytics` `Business Intelligence` `Data Visualization` `Dashboard Design` `Data Modeling` `Portfolio Project` `Entertainment Analytics` `KPI Tracking`

---

### ⭐ If you found this project helpful, please give it a star!

---

**Last Updated:** November 2024  
**Version:** 1.0.0
