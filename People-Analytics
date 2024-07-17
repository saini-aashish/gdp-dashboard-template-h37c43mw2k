import streamlit as st
import pandas as pd
import datetime
import altair as alt

# Set the title and favicon for the Browser's tab.
st.set_page_config(
    page_title='Sales Dashboard',
    page_icon=':bar_chart:',
    layout='wide'
)

# Sample data for the Sales Dashboard
data = {
    "Month": ["Jan", "Feb", "Mar", "Jan", "Feb", "Mar", "Jan", "Feb", "Mar"],
    "Year": ["2022", "2022", "2022", "2023", "2023", "2023", "2024", "2024", "2024"],
    "Region": ["North", "North", "South", "North", "South", "South", "North", "North", "South"],
    "Category": ["Electronics", "Furniture", "Electronics", "Furniture", "Electronics", "Furniture", "Electronics", "Furniture", "Electronics"],
    "Sales": [20000, 30000, 25000, 22000, 32000, 27000, 24000, 34000, 29000],
    "Profit": [5000, 7000, 6000, 5500, 7500, 6500, 5800, 7800, 6800]
}

df = pd.DataFrame(data)
df['Date'] = pd.to_datetime(df['Month'] + '-' + df['Year'])

# Function to reset filters
def reset_filters():
    st.session_state['year'] = "2024"
    st.session_state['selected_category'] = "All"
    st.session_state['selected_region'] = "All"

# Initialize session state
if 'year' not in st.session_state:
    st.session_state['year'] = "2024"
if 'selected_category' not in st.session_state:
    st.session_state['selected_category'] = "All"
if 'selected_region' not in st.session_state:
    st.session_state['selected_region'] = "All"

# Page title
st.title("Sales Dashboard")

# Today's date
today_date = datetime.datetime.now().strftime("%d/%m/%Y")

# Header
st.markdown(f"""
    <style>
        .header {{
            background-color: #29BA74;
            padding: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }}
        .header img {{
            max-height: 50px;
        }}
        .header h1 {{
            color: white;
            margin: 0;
            flex-grow: 1;
            text-align: center;
        }}
        .header p {{
            color: white;
            margin: 0;
            font-size: small;
        }}
    </style>
    <div class="header">
        <img src="https://sloanreview.mit.edu/wp-content/uploads/2019/03/BCG-2019-white.png">
        <h1>Sales Dashboard</h1>
        <p>Last updated date: {today_date}</p>
    </div>
    """, unsafe_allow_html=True)

# Filters
st.sidebar.header("Filters")
try:
    year = st.sidebar.selectbox("Year", ["2022", "2023", "2024"], index=["2022", "2023", "2024"].index(st.session_state['year']))
    selected_category = st.sidebar.selectbox("Category", ["All", "Electronics", "Furniture"], index=["All", "Electronics", "Furniture"].index(st.session_state['selected_category']))
    selected_region = st.sidebar.selectbox("Region", ["All", "North", "South"], index=["All", "North", "South"].index(st.session_state['selected_region']))
except Exception as e:
    st.sidebar.error(f"Error in filter selection: {e}")

# Reset filters button
if st.sidebar.button("Reset Filters"):
    reset_filters()
    st.experimental_rerun()

# Apply filters
filtered_df = df.copy()
try:
    if selected_category != "All":
        filtered_df = filtered_df[filtered_df["Category"] == selected_category]
    if selected_region != "All":
        filtered_df = filtered_df[filtered_df["Region"] == selected_region]
except Exception as e:
    st.error(f"Error in applying filters: {e}")

st.session_state.update({
    'year': year,
    'selected_category': selected_category,
    'selected_region': selected_region
})

# Section 1: KPI Boxes
st.header("Key Performance Indicators")
try:
    sales_this_year = filtered_df[filtered_df["Year"] == year]["Sales"].sum()
    prev_sales = filtered_df[filtered_df["Year"] == str(int(year)-1)]["Sales"].sum()
    total_profits = filtered_df[filtered_df["Year"] == year]["Profit"].sum()
    profit_loss_percentage = ((total_profits - prev_sales) / prev_sales) * 100 if prev_sales else 0

    kpi1, kpi2, kpi3 = st.columns(3)
    kpi1.metric("Sales This Year", f"${sales_this_year:,.2f}")
    kpi2.metric("Profit/Loss Percentage", f"{profit_loss_percentage:.2f}%")
    kpi3.metric("Total Profits", f"${total_profits:,.2f}")
except Exception as e:
    st.error(f"Error in KPI calculation: {e}")

# Section 2: Year on Year Comparison (Line Chart)
st.header("Year on Year Sales Comparison")
try:
    line_chart_data = filtered_df[filtered_df["Year"].isin(["2022", "2023", "2024"])].copy()
    line_chart = alt.Chart(line_chart_data).mark_line(point=True).encode(
        x='Month:N',
        y='Sales:Q',
        color='Year:N',
        tooltip=['Month', 'Sales', 'Year']
    ).properties(
        width=700,
        height=400
    ).configure_mark(opacity=0.6).configure_axis(
        labelColor='#29BA74', titleColor='#29BA74'
    ).configure_legend(titleColor='#29BA74', labelColor='#29BA74')
    st.altair_chart(line_chart, use_container_width=True)
except Exception as e:
    st.error(f"Error in line chart generation: {e}")

# Section 3: Category-wise Split (Donut Chart)
st.header("Category-wise Sales Split")
try:
    pie_chart_data = filtered_df.groupby("Category")["Sales"].sum().reset_index()
    donut_chart = alt.Chart(pie_chart_data).mark_arc(innerRadius=100).encode(
        theta='Sales',
        color=alt.Color('Category', scale=alt.Scale(range=['#29BA74', '#66cdaa']))
    ).properties(width=400, height=400)
    st.altair_chart(donut_chart, use_container_width=True)
except Exception as e:
    st.error(f"Error in donut chart generation: {e}")

# Section 4: Regional Profits (Bar Chart)
st.header("Regional Profits")
try:
    bar_chart_data = filtered_df.groupby("Region")["Profit"].sum().reset_index()
    bar_chart = alt.Chart(bar_chart_data).mark_bar().encode(
        x='Profit:Q', y='Region:N', color=alt.Color('Region', scale=alt.Scale(range=['#29BA74', '#66cdaa']))
    ).properties(width=400, height=400)
    st.altair_chart(bar_chart, use_container_width=True)
except Exception as e:
    st.error(f"Error in bar chart generation: {e}")

# Footer
st.markdown("""
    <style>
        .footer {
            background-color: #29BA74;
            padding: 10px;
            text-align: right;
        }
        .footer p {
            color: white;
            margin: 0;
        }
    </style>
    <div class="footer">
        <p>Powered by Global People Analytics Team</p>
    </div>
    """, unsafe_allow_html=True)
