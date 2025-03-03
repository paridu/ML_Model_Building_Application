import streamlit as st
import pandas as pd
import numpy as np
import plotly.express as px
import plotly.graph_objects as go
from datetime import datetime, timedelta
import altair as alt

# ตั้งค่าหน้าเว็บ
st.set_page_config(
    page_title="ระบบ Monitoring KPI สำหรับการปลูกผักไฮโดรโปนิก",
    page_icon="🌱",
    layout="wide",
    initial_sidebar_state="expanded"
)

# สร้างข้อมูลจำลอง
@st.cache_data
def generate_sample_data():
    # สร้างวันที่ย้อนหลัง 30 วัน
    dates = [(datetime.now() - timedelta(days=i)).strftime("%Y-%m-%d") for i in range(30)]
    dates.reverse()
    
    # สร้างข้อมูลเซ็นเซอร์จำลอง
    np.random.seed(42)
    data = {
        'วันที่': dates,
        'EC (mS/cm)': np.random.normal(1.8, 0.2, 30).round(2),
        'pH': np.random.normal(6.0, 0.3, 30).round(2),
        'อุณหภูมิน้ำ (°C)': np.random.normal(25, 1.5, 30).round(1),
        'อุณหภูมิอากาศ (°C)': np.random.normal(28, 2, 30).round(1),
        'ความชื้น (%)': np.random.normal(70, 5, 30).round(1),
        'ระดับน้ำ (cm)': np.random.normal(15, 1, 30).round(1),
        'ปริมาณแสง (lux)': np.random.normal(12000, 2000, 30).round(0),
        'ออกซิเจนในน้ำ (mg/L)': np.random.normal(7, 0.5, 30).round(1)
    }
    
    # ข้อมูลพืช
    plant_data = {
        'พืช': ['ผักกาดหอม', 'คะน้า', 'ผักบุ้ง', 'ผักสลัด', 'ผักโขม'],
        'จำนวนต้น': [120, 100, 80, 150, 90],
        'อัตราการงอก (%)': [95, 92, 98, 94, 91],
        'จำนวนวันก่อนเก็บเกี่ยว': [10, 5, 8, 12, 15],
        'ผลผลิตคาดการณ์ (kg)': [24, 30, 16, 22.5, 18]
    }
    
    # ข้อมูลผลผลิตที่ผ่านมา
    harvest_data = {
        'วันที่': [(datetime.now() - timedelta(days=i*10)).strftime("%Y-%m-%d") for i in range(5)],
        'พืช': ['ผักกาดหอม', 'คะน้า', 'ผักบุ้ง', 'ผักสลัด', 'ผักโขม'],
        'ผลผลิตจริง (kg)': [22.5, 28.7, 15.2, 21.8, 17.4],
        'ผลผลิตคาดการณ์ (kg)': [24, 30, 16, 22.5, 18],
        'ต้นทุน (บาท)': [1200, 1100, 900, 1300, 950],
        'รายได้ (บาท)': [2700, 3444, 1824, 2616, 2088]
    }
    
    # ประสิทธิภาพระบบ
    system_data = {
        'อุปกรณ์': ['ปั๊มน้ำ', 'ระบบให้แสง', 'ระบบให้อากาศ', 'ระบบควบคุม pH/EC'],
        'สถานะ': ['ปกติ', 'ปกติ', 'ปกติ', 'ปกติ'],
        'ประสิทธิภาพ (%)': [98, 95, 97, 96],
        'ชั่วโมงทำงาน': [720, 360, 720, 720],
        'การใช้พลังงาน (kWh)': [21.6, 108, 14.4, 7.2]
    }
    
    return pd.DataFrame(data), pd.DataFrame(plant_data), pd.DataFrame(harvest_data), pd.DataFrame(system_data)

# โหลดข้อมูลจำลอง
sensor_data, plant_data, harvest_data, system_data = generate_sample_data()

# สร้าง sidebar สำหรับเมนู
st.sidebar.title("🌱 ระบบ Monitoring KPI")
menu = st.sidebar.radio(
    "เลือกเมนู:",
    ["Dashboard หลัก", "ข้อมูลเซ็นเซอร์", "การปลูกพืช", "ผลผลิตและกำไร", "ประสิทธิภาพระบบ", "การแจ้งเตือน", "การตั้งค่า KPI"]
)

# ฟังก์ชัน Dashboard หลัก
if menu == "Dashboard หลัก":
    st.title("Dashboard ระบบปลูกผักไฮโดรโปนิก")
    
    # สร้าง KPI cards
    col1, col2, col3, col4 = st.columns(4)
    
    with col1:
        st.metric(
            label="ค่า EC เฉลี่ย (mS/cm)",
            value=f"{sensor_data['EC (mS/cm)'].iloc[-1]:.2f}",
            delta=f"{sensor_data['EC (mS/cm)'].iloc[-1] - sensor_data['EC (mS/cm)'].iloc[-2]:.2f}"
        )
    
    with col2:
        st.metric(
            label="ค่า pH เฉลี่ย",
            value=f"{sensor_data['pH'].iloc[-1]:.2f}",
            delta=f"{sensor_data['pH'].iloc[-1] - sensor_data['pH'].iloc[-2]:.2f}"
        )
    
    with col3:
        st.metric(
            label="อุณหภูมิน้ำ (°C)",
            value=f"{sensor_data['อุณหภูมิน้ำ (°C)'].iloc[-1]:.1f}",
            delta=f"{sensor_data['อุณหภูมิน้ำ (°C)'].iloc[-1] - sensor_data['อุณหภูมิน้ำ (°C)'].iloc[-2]:.1f}"
        )
    
    with col4:
        # คำนวณกำไรจากข้อมูล
        profit = (harvest_data['รายได้ (บาท)'].iloc[-1] - harvest_data['ต้นทุน (บาท)'].iloc[-1])
        previous_profit = (harvest_data['รายได้ (บาท)'].iloc[-2] - harvest_data['ต้นทุน (บาท)'].iloc[-2])
        st.metric(
            label="กำไรรอบล่าสุด (บาท)",
            value=f"{profit:,.0f}",
            delta=f"{profit - previous_profit:,.0f}"
        )
    
    # กราฟแสดงค่า EC และ pH ย้อนหลัง 30 วัน
    st.subheader("ค่า EC และ pH ย้อนหลัง 30 วัน")
    
    fig = go.Figure()
    fig.add_trace(go.Scatter(
        x=sensor_data['วันที่'],
        y=sensor_data['EC (mS/cm)'],
        name='EC (mS/cm)',
        line=dict(color='blue')
    ))
    
    fig.add_trace(go.Scatter(
        x=sensor_data['วันที่'],
        y=sensor_data['pH'],
        name='pH',
        line=dict(color='green'),
        yaxis='y2'
    ))
    
    fig.update_layout(
        title='ค่า EC และ pH',
        xaxis=dict(title='วันที่'),
        yaxis=dict(title='EC (mS/cm)', titlefont=dict(color='blue'), tickfont=dict(color='blue')),
        yaxis2=dict(title='pH', titlefont=dict(color='green'), tickfont=dict(color='green'),
                  anchor='x', overlaying='y', side='right'),
        legend=dict(orientation='h', yanchor='bottom', y=1.02, xanchor='right', x=1)
    )
    
    st.plotly_chart(fig, use_container_width=True)
    
    # แสดงข้อมูลพืชที่กำลังปลูก
    st.subheader("พืชที่กำลังปลูก")
    col1, col2 = st.columns(2)
    
    with col1:
        fig_plants = px.bar(
            plant_data,
            x='พืช',
            y='จำนวนต้น',
            color='อัตราการงอก (%)',
            text='จำนวนต้น',
            color_continuous_scale='Viridis'
        )
        fig_plants.update_traces(texttemplate='%{text}', textposition='outside')
        st.plotly_chart(fig_plants, use_container_width=True)
    
    with col2:
        fig_harvest = px.bar(
            plant_data,
            x='พืช',
            y='ผลผลิตคาดการณ์ (kg)',
            color='จำนวนวันก่อนเก็บเกี่ยว',
            text='ผลผลิตคาดการณ์ (kg)',
            color_continuous_scale='Viridis_r'
        )
        fig_harvest.update_traces(texttemplate='%{text} kg', textposition='outside')
        st.plotly_chart(fig_harvest, use_container_width=True)
    
    # แสดงการแจ้งเตือนล่าสุด
    st.subheader("การแจ้งเตือนล่าสุด")
    alerts = [
        {"timestamp": "2023-09-30 08:15", "level": "warning", "message": "ค่า pH ต่ำกว่าเกณฑ์ (5.4)"},
        {"timestamp": "2023-09-29 14:30", "level": "info", "message": "เติมสารอาหารแล้ว"},
        {"timestamp": "2023-09-28 20:45", "level": "critical", "message": "ระดับน้ำต่ำกว่าเกณฑ์"},
    ]
    
    for alert in alerts:
        if alert["level"] == "critical":
            st.error(f"{alert['timestamp']} - {alert['message']}")
        elif alert["level"] == "warning":
            st.warning(f"{alert['timestamp']} - {alert['message']}")
        else:
            st.info(f"{alert['timestamp']} - {alert['message']}")

# ฟังก์ชัน ข้อมูลเซ็นเซอร์
elif menu == "ข้อมูลเซ็นเซอร์":
    st.title("ข้อมูลเซ็นเซอร์")
    
    # เลือกเซ็นเซอร์ที่ต้องการดู
    sensors = [col for col in sensor_data.columns if col != 'วันที่']
    selected_sensors = st.multiselect(
        "เลือกเซ็นเซอร์ที่ต้องการดู",
        sensors,
        default=["EC (mS/cm)", "pH", "อุณหภูมิน้ำ (°C)"]
    )
    
    # เลือกช่วงเวลา
    date_range = st.date_input(
        "เลือกช่วงเวลา",
        [datetime.strptime(sensor_data['วันที่'].iloc[0], "%Y-%m-%d").date(),
         datetime.strptime(sensor_data['วันที่'].iloc[-1], "%Y-%m-%d").date()]
    )
    
    if selected_sensors:
        # กรองข้อมูลตามช่วงเวลา
        start_date = date_range[0].strftime("%Y-%m-%d")
        end_date = date_range[1].strftime("%Y-%m-%d") if len(date_range) > 1 else start_date
        filtered_data = sensor_data[(sensor_data['วันที่'] >= start_date) & (sensor_data['วันที่'] <= end_date)]
        
        # แสดงกราฟ
        fig = px.line(
            filtered_data,
            x='วันที่',
            y=selected_sensors,
            title="ข้อมูลเซ็นเซอร์ตามช่วงเวลา",
            markers=True
        )
        st.plotly_chart(fig, use_container_width=True)
        
        # แสดงค่าสถิติพื้นฐาน
        st.subheader("ค่าสถิติพื้นฐาน")
        stats = filtered_data[selected_sensors].describe().round(2)
        st.dataframe(stats, use_container_width=True)
        
        # แสดงข้อมูลดิบ
        st.subheader("ข้อมูลดิบ")
        st.dataframe(filtered_data[['วันที่'] + selected_sensors], use_container_width=True)
        
        # ตั้งค่าการแจ้งเตือนสำหรับเซ็นเซอร์
        st.subheader("ตั้งค่าการแจ้งเตือน")
        
        alert_settings = {
            "EC (mS/cm)": {"min": 1.0, "max": 2.5},
            "pH": {"min": 5.5, "max": 6.5},
            "อุณหภูมิน้ำ (°C)": {"min": 18.0, "max": 30.0},
            "อุณหภูมิอากาศ (°C)": {"min": 18.0, "max": 35.0},
            "ความชื้น (%)": {"min": 60.0, "max": 80.0},
            "ระดับน้ำ (cm)": {"min": 10.0, "max": None},
            "ปริมาณแสง (lux)": {"min": 8000, "max": 16000},
            "ออกซิเจนในน้ำ (mg/L)": {"min": 5.0, "max": None}
        }
        
        selected_sensor = st.selectbox("เลือกเซ็นเซอร์เพื่อตั้งค่าการแจ้งเตือน", selected_sensors)
        
        col1, col2 = st.columns(2)
        with col1:
            min_value = st.number_input(
                f"ค่าต่ำสุดสำหรับ {selected_sensor}",
                value=float(alert_settings[selected_sensor]["min"]) if alert_settings[selected_sensor]["min"] else 0.0,
                step=0.1
            )
        
        with col2:
            max_value = st.number_input(
                f"ค่าสูงสุดสำหรับ {selected_sensor}",
                value=float(alert_settings[selected_sensor]["max"]) if alert_settings[selected_sensor]["max"] else 0.0,
                step=0.1
            )
        
        if st.button("บันทึกการตั้งค่า"):
            st.success(f"บันทึกการตั้งค่าสำหรับ {selected_sensor} เรียบร้อยแล้ว (ค่าต่ำสุด: {min_value}, ค่าสูงสุด: {max_value})")

# ฟังก์ชัน การปลูกพืช
elif menu == "การปลูกพืช":
    st.title("ข้อมูลการปลูกพืช")
    
    # แสดงข้อมูลพืชที่กำลังปลูก
    st.subheader("พืชที่กำลังปลูกในปัจจุบัน")
    st.dataframe(plant_data, use_container_width=True)
    
    # เพิ่มรายการปลูกใหม่
    st.subheader("เพิ่มรายการปลูกใหม่")
    
    col1, col2, col3 = st.columns(3)
    
    with col1:
        new_plant = st.text_input("ชื่อพืช")
        plant_amount = st.number_input("จำนวนต้น", min_value=1, value=100)
    
    with col2:
        germination_rate = st.slider("อัตราการงอก (%)", min_value=50, max_value=100, value=95)
        harvest_days = st.number_input("จำนวนวันก่อนเก็บเกี่ยว", min_value=1, max_value=60, value=14)
    
    with col3:
        expected_yield = st.number_input("ผลผลิตคาดการณ์ (kg)", min_value=0.1, value=20.0, step=0.1)
        planting_date = st.date_input("วันที่ปลูก", datetime.now())
    
    if st.button("เพิ่มรายการปลูก"):
        if new_plant:
            st.success(f"เพิ่มรายการปลูก {new_plant} จำนวน {plant_amount} ต้น เรียบร้อยแล้ว")
            
            # แสดงแผนการปลูกและคาดการณ์การเก็บเกี่ยว
            expected_harvest_date = planting_date + timedelta(days=harvest_days)
            st.info(f"วันที่คาดว่าจะเก็บเกี่ยว: {expected_harvest_date.strftime('%Y-%m-%d')}")
            
            # แนะนำการตั้งค่าสารอาหารที่เหมาะสม
            st.subheader("คำแนะนำการตั้งค่าสารอาหาร")
            
            recommended_settings = {
                "ผักกาดหอม": {"EC": "1.4-1.8", "pH": "5.8-6.2"},
                "คะน้า": {"EC": "1.8-2.2", "pH": "6.0-6.5"},
                "ผักบุ้ง": {"EC": "1.2-1.6", "pH": "5.8-6.2"},
                "ผักสลัด": {"EC": "1.0-1.4", "pH": "5.8-6.0"},
                "ผักโขม": {"EC": "1.8-2.2", "pH": "6.0-6.5"}
            }
            
            # สุ่มค่าแนะนำหากไม่มีในรายการ
            if new_plant not in recommended_settings:
                recommended_ec = f"{np.random.uniform(1.2, 2.2):.1f}-{np.random.uniform(1.4, 2.4):.1f}"
                recommended_ph = f"{np.random.uniform(5.5, 6.2):.1f}-{np.random.uniform(6.0, 6.5):.1f}"
            else:
                recommended_ec = recommended_settings[new_plant]["EC"]
                recommended_ph = recommended_settings[new_plant]["pH"]
            
            col1, col2 = st.columns(2)
            with col1:
                st.metric("ค่า EC ที่แนะนำ (mS/cm)", recommended_ec)
            with col2:
                st.metric("ค่า pH ที่แนะนำ", recommended_ph)
        else:
            st.error("กรุณาระบุชื่อพืช")
    
    # แสดงข้อมูลการเก็บเกี่ยวที่ผ่านมา
    st.subheader("ประวัติการเก็บเกี่ยว")
    st.dataframe(harvest_data, use_container_width=True)
    
    # กราฟเปรียบเทียบผลผลิตจริงกับคาดการณ์
    fig = px.bar(
        harvest_data,
        x='พืช',
        y=['ผลผลิตจริง (kg)', 'ผลผลิตคาดการณ์ (kg)'],
        barmode='group',
        title="เปรียบเทียบผลผลิตจริงกับคาดการณ์",
        labels={'value': 'ผลผลิต (kg)', 'variable': 'ประเภท'}
    )
    st.plotly_chart(fig, use_container_width=True)

# ฟังก์ชัน ผลผลิตและกำไร
elif menu == "ผลผลิตและกำไร":
    st.title("ข้อมูลผลผลิตและกำไร")
    
    # แสดงสรุปผลผลิตและกำไร
    st.subheader("สรุปผลผลิตและกำไร")
    
    # คำนวณกำไรและ ROI
    harvest_data['กำไร (บาท)'] = harvest_data['รายได้ (บาท)'] - harvest_data['ต้นทุน (บาท)']
    harvest_data['ROI (%)'] = (harvest_data['กำไร (บาท)'] / harvest_data['ต้นทุน (บาท)'] * 100).round(1)
    
    # แสดง KPI การผลิต
    col1, col2, col3, col4 = st.columns(4)
    
    with col1:
        total_yield = harvest_data['ผลผลิตจริง (kg)'].sum()
        st.metric("ผลผลิตรวม (kg)", f"{total_yield:.1f}")
    
    with col2:
        total_cost = harvest_data['ต้นทุน (บาท)'].sum()
        st.metric("ต้นทุนรวม (บาท)", f"{total_cost:,.0f}")
    
    with col3:
        total_revenue = harvest_data['รายได้ (บาท)'].sum()
        st.metric("รายได้รวม (บาท)", f"{total_revenue:,.0f}")
    
    with col4:
        total_profit = harvest_data['กำไร (บาท)'].sum()
        avg_roi = harvest_data['ROI (%)'].mean()
        st.metric("กำไรรวม (บาท)", f"{total_profit:,.0f}", f"ROI เฉลี่ย {avg_roi:.1f}%")
    
    # กราฟแสดงผลผลิตตามพืช
    fig_yield = px.pie(
        harvest_data,
        values='ผลผลิตจริง (kg)',
        names='พืช',
        title="สัดส่วนผลผลิตตามพืช"
    )
    st.plotly_chart(fig_yield, use_container_width=True)
    
    # กราฟแสดงต้นทุน รายได้ และกำไร
    fig_profit = px.bar(
        harvest_data,
        x='พืช',
        y=['ต้นทุน (บาท)', 'รายได้ (บาท)', 'กำไร (บาท)'],
        barmode='group',
        title="เปรียบเทียบต้นทุน รายได้ และกำไร",
        labels={'value': 'จำนวนเงิน (บาท)', 'variable': 'ประเภท'}
    )
    st.plotly_chart(fig_profit, use_container_width=True)
    
    # กราฟแสดง ROI
    fig_roi = px.bar(
        harvest_data,
        x='พืช',
        y='ROI (%)',
        title="ROI ของแต่ละพืช",
        color='ROI (%)',
        color_continuous_scale='RdYlGn'
    )
    st.plotly_chart(fig_roi, use_container_width=True)
    
    # บันทึกข้อมูลการเก็บเกี่ยวใหม่
    st.subheader("บันทึกข้อมูลการเก็บเกี่ยวใหม่")
    
    col1, col2 = st.columns(2)
    
    with col1:
        harvest_date = st.date_input("วันที่เก็บเกี่ยว", datetime.now())
        plant_type = st.selectbox("พืช", plant_data['พืช'].tolist())
        actual_yield = st.number_input("ผลผลิตจริง (kg)", min_value=0.1, value=20.0, step=0.1)
    
    with col2:
        expected_yield = st.number_input("ผลผลิตคาดการณ์ (kg)", min_value=0.1, value=20.0, step=0.1)
        cost = st.number_input("ต้นทุน (บาท)", min_value=0, value=1000, step=100)
        revenue = st.number_input("รายได้ (บาท)", min_value=0, value=1500, step=100)
    
    if st.button("บันทึกข้อมูลการเก็บเกี่ยว"):
        profit = revenue - cost
        roi = profit / cost * 100 if cost > 0 else 0
        st.success(f"บันทึกข้อมูลการเก็บเกี่ยว {plant_type} เรียบร้อยแล้ว")
        
        # แสดงผลลัพธ์
        st.info(f"กำไร: {profit:,.0f} บาท (ROI: {roi:.1f}%)")
        
        if actual_yield < expected_yield * 0.9:
            st.warning(f"ผลผลิตจริงต่ำกว่าคาดการณ์ {(expected_yield - actual_yield):.1f} kg ({((expected_yield - actual_yield) / expected_yield * 100):.1f}%)")
        elif actual_yield > expected_yield * 1.1:
            st.success(f"ผลผลิตจริงสูงกว่าคาดการณ์ {(actual_yield - expected_yield):.1f} kg ({((actual_yield - expected_yield) / expected_yield * 100):.1f}%)")

# ฟังก์ชัน ประสิทธิภาพระบบ
elif menu == "ประสิทธิภาพระบบ":
    st.title("ประสิทธิภาพระบบและการใช้พลังงาน")
    
    # แสดงข้อมูลประสิทธิภาพระบบ
    st.subheader("ข้อมูลประสิทธิภาพระบบ")
    
    # สร้าง DataFrame ระบบ
    system_status = system_data.copy()
    
    # แสดงสถานะระบบ
    for i, row in system_status.iterrows():
        col1, col2, col3 = st.columns([1, 3, 2])
        
        with col1:
            st.subheader(row['อุปกรณ์'])
        
        with col2:
            if row['สถานะ'] == 'ปกติ':
                st.success(f"สถานะ: {row['สถานะ']} (ประสิทธิภาพ: {row['ประสิทธิภาพ (%)']}%)")
        
        with col3:
            st.info(f"ชั่วโมงทำงาน: {row['ชั่วโมงทำงาน']} ชม.")
    
    # แสดงกราฟประสิทธิภาพระบบ
    fig = px.bar(
        system_status,
        x='อุปกรณ์',
        y='ประสิทธิภาพ (%)',
        title="ประสิทธิภาพของอุปกรณ์",
        color='ประสิทธิภาพ (%)',
        color_continuous_scale='RdYlGn',
        range_color=[80, 100]
    )
    fig.update_layout(yaxis_range=[80, 100])
    st.plotly_chart(fig, use_container_width=True)
    
    # แสดงการใช้พลังงาน
    st.subheader("การใช้พลังงาน")
    
    energy_fig = px.pie(
        system_status,
        values='การใช้พลังงาน (kWh)',
        names='อุปกรณ์',
        title="สัดส่วนการใช้พลังงานของอุปกรณ์"
    )
    st.plotly_chart(energy_fig, use_container_width=True)
    
    # คำนวณต้นทุนพลังงาน
    energy_price = st.slider("ราคาไฟฟ้าต่อหน่วย (บาท/kWh)", min_value=3.0, max_value=5.0, value=4.0, step=0.1)
    
    total_energy = system_status['การใช้พลังงาน (kWh)'].sum()
    energy_cost = total_energy * energy_price
    
    st.metric("การใช้พลังงานรวม (kWh)", f"{total_energy:.1f}", f"ค่าไฟฟ้าประมาณ {energy_cost:.1f} บาท")
    
    # บันทึกการบำรุงรักษาระบบ
    st.subheader("บันทึกการบำรุงรักษาระบบ")
    
    col1, col2 = st.columns(2)
    
    with col1:
        equipment = st.selectbox("อุปกรณ์", system_status['อุปกรณ์'].tolist())
        maintenance_date = st.date_input("วันที่บำรุงรักษา", datetime.now())
    
    with col2:
        maintenance_type = st.selectbox("ประเภทการบำรุงรักษา", ["การทำความสะอาด", "การเปลี่ยนอะไหล่", "การสอบเทียบ", "การซ่อมแซม", "อื่นๆ"])
        maintenance_note = st.text_area("รายละเอียด", "")
    
    if st.button("บันทึกการบำรุงรักษา"):
        st.success(f"บันทึกการบำรุงรักษา {equipment} เรียบร้อยแล้ว")
        
        # แนะนำการบำรุงรักษาถัดไป
        next_maintenance = maintenance_date + timedelta(days=30)
        st.info(f"ควรทำการบำรุงรักษาครั้งถัดไปภายในวันที่ {next_maintenance.strftime('%Y-%m-%d')}")

# ฟังก์ชัน การแจ้งเตือน
elif menu == "การแจ้งเตือน":
    st.title("ระบบการแจ้งเตือน")
    
    # สร้างข้อมูลการแจ้งเตือนจำลอง
    alerts_data = [
        {"id": 1, "timestamp": "2023-09-30 08:15:23", "level": "warning", "source": "เซ็นเซอร์ pH", "message": "ค่า pH ต่ำกว่าเกณฑ์ (5.4)", "status": "แก้ไขแล้ว"},
        {"id": 2, "timestamp": "2023-09-29 14:30:45", "level": "info", "source": "ระบบอัตโนมัติ", "message": "เติมสารอาหารแล้ว", "status": "เสร็จสิ้น"},
        {"id": 3, "timestamp": "2023-09-28 20:45:12", "level": "critical", "source": "เซ็นเซอร์ระดับน้ำ", "message": "ระดับน้ำต่ำกว่าเกณฑ์", "status": "แก้ไขแล้ว"},
        {"id": 4, "timestamp": "2023-09-28 15:30:22", "level": "warning", "source": "เซ็นเซอร์อุณหภูมิ", "message": "อุณหภูมิน้ำสูงเกินเกณฑ์ (32.5°C)", "status": "แก้ไขแล้ว"},
        {"id": 5, "timestamp": "2023-09-27 09:10:45", "level": "critical", "source": "ระบบให้อากาศ", "message": "ปั๊มอากาศไม่ทำงาน", "status": "แก้ไขแล้ว"},
        {"id": 6, "timestamp": "2023-09-26 22:05:18", "level": "info", "source": "ระบบอัตโนมัติ", "message": "ปรับค่า EC อัตโนมัติ", "status": "เสร็จสิ้น"},
        {"id": 7, "timestamp": "2023-09-26 10:40:03", "level": "warning", "source": "เซ็นเซอร์ EC", "message": "ค่า EC สูงเกินเกณฑ์ (2.8 mS/cm)", "status": "แก้ไขแล้ว"},
    ]
    
    alerts_df = pd.DataFrame(alerts_data)
    
    # แสดงการแจ้งเตือนล่าสุด
    st.subheader("การแจ้งเตือนทั้งหมด")
    
    # ตัวกรองการแจ้งเตือน
    col1, col2, col3 = st.columns(3)
    
    with col1:
        filter_level = st.multiselect("ระดับการแจ้งเตือน", ["critical", "warning", "info"], default=["critical", "warning", "info"])
    
    with col2:
        filter_source = st.multiselect("แหล่งที่มา", alerts_df['source'].unique().tolist(), default=alerts_df['source'].unique().tolist())
    
    with col3:
        filter_status = st.multiselect("สถานะ", alerts_df['status'].unique().tolist(), default=alerts_df['status'].unique().tolist())
    
    # กรองข้อมูล
    filtered_alerts = alerts_df[
        alerts_df['level'].isin(filter_level) &
        alerts_df['source'].isin(filter_source) &
        alerts_df['status'].isin(filter_status)
    ]
    
    # แสดงการแจ้งเตือน
    for i, alert in filtered_alerts.iterrows():
        col1, col2 = st.columns([3, 1])
        
        with col1:
            if alert["level"] == "critical":
                st.error(f"{alert['timestamp']} - {alert['message']} [{alert['source']}]")
            elif alert["level"] == "warning":
                st.warning(f"{alert['timestamp']} - {alert['message']} [{alert['source']}]")
            else:
                st.info(f"{alert['timestamp']} - {alert['message']} [{alert['source']}]")
        
        with col2:
            st.text(f"สถานะ: {alert['status']}")
    
    # ตั้งค่าการแจ้งเตือน
    st.subheader("ตั้งค่าการแจ้งเตือน")
    
    st.write("กำหนดช่องทางการรับการแจ้งเตือน")
    
    notification_settings = {
        "critical": {"email": True, "sms": True, "line": True, "app": True},
        "warning": {"email": True, "sms": False, "line": True, "app": True},
        "info": {"email": False, "sms": False, "line": False, "app": True}
    }
    
    for level, channels in notification_settings.items():
        st.write(f"**ระดับ {level}:**")
        
        col1, col2, col3, col4 = st.columns(4)
        
        with col1:
            channels["email"] = st.checkbox(f"Email", value=channels["email"], key=f"{level}_email")
        
        with col2:
            channels["sms"] = st.checkbox(f"SMS", value=channels["sms"], key=f"{level}_sms")
        
        with col3:
            channels["line"] = st.checkbox(f"Line", value=channels["line"], key=f"{level}_line")
        
        with col4:
            channels["app"] = st.checkbox(f"แอปพลิเคชัน", value=channels["app"], key=f"{level}_app")
    
    if st.button("บันทึกการตั้งค่าการแจ้งเตือน"):
        st.success("บันทึกการตั้งค่าการแจ้งเตือนเรียบร้อยแล้ว")
    
    # ทดสอบการแจ้งเตือน
    st.subheader("ทดสอบการแจ้งเตือน")
    
    col1, col2 = st.columns(2)
    
    with col1:
        test_level = st.selectbox("ระดับการแจ้งเตือนทดสอบ", ["critical", "warning", "info"])
        test_source = st.selectbox("แหล่งที่มา", ["เซ็นเซอร์ pH", "เซ็นเซอร์ EC", "เซ็นเซอร์อุณหภูมิ", "ระบบให้อากาศ", "ระบบอัตโนมัติ"])
    
    with col2:
        test_message = st.text_input("ข้อความทดสอบ", "ทดสอบการแจ้งเตือน")
    
    if st.button("ทดสอบการแจ้งเตือน"):
        st.info(f"ทดสอบการแจ้งเตือนระดับ {test_level} ผ่านช่องทาง: " + 
                ", ".join([channel for channel, enabled in notification_settings[test_level].items() if enabled]))
        
        if test_level == "critical":
            st.error(f"ทดสอบ: {test_message} [{test_source}]")
        elif test_level == "warning":
            st.warning(f"ทดสอบ: {test_message} [{test_source}]")
        else:
            st.info(f"ทดสอบ: {test_message} [{test_source}]")

# ฟังก์ชัน การตั้งค่า KPI
elif menu == "การตั้งค่า KPI":
    st.title("การตั้งค่า KPI และเป้าหมาย")
    
    # สร้างข้อมูล KPI จำลอง
    kpi_data = {
        "KPI": [
            "ผลผลิตต่อพื้นที่ (kg/m²)",
            "ประสิทธิภาพการใช้น้ำ (kg/L)",
            "ประสิทธิภาพการใช้พลังงาน (kg/kWh)",
            "อัตราการเติบโตของพืช (cm/วัน)",
            "อัตรากำไรขั้นต้น (%)",
            "ROI (%)"
        ],
        "เป้าหมาย": [4.0, 0.05, 0.8, 1.2, 40.0, 35.0],
        "ปัจจุบัน": [3.8, 0.048, 0.75, 1.1, 38.5, 33.2],
        "หน่วย": ["kg/m²", "kg/L", "kg/kWh", "cm/วัน", "%", "%"]
    }
    
    kpi_df = pd.DataFrame(kpi_data)
    
    # คำนวณร้อยละความสำเร็จ
    kpi_df["ความสำเร็จ (%)"] = (kpi_df["ปัจจุบัน"] / kpi_df["เป้าหมาย"] * 100).round(1)
    
    # สร้างกราฟ KPI แบบเกจ
    st.subheader("แดชบอร์ด KPI")
    
    # สร้างกราฟแท่งเปรียบเทียบเป้าหมายกับปัจจุบัน
    fig = go.Figure()
    
    for i, kpi in enumerate(kpi_df["KPI"]):
        fig.add_trace(go.Bar(
            name="เป้าหมาย",
            x=[kpi],
            y=[kpi_df["เป้าหมาย"][i]],
            marker_color='green',
            opacity=0.5
        ))
        
        fig.add_trace(go.Bar(
            name="ปัจจุบัน",
            x=[kpi],
            y=[kpi_df["ปัจจุบัน"][i]],
            marker_color='blue'
        ))
        
        # เพิ่มข้อความแสดงค่า
        fig.add_annotation(
            x=kpi,
            y=kpi_df["ปัจจุบัน"][i],
            text=f"{kpi_df['ปัจจุบัน'][i]} {kpi_df['หน่วย'][i]}<br>({kpi_df['ความสำเร็จ (%)'][i]}%)",
            showarrow=True,
            arrowhead=2,
            arrowcolor="black",
            arrowsize=1,
            ax=0,
            ay=-40
        )
    
    fig.update_layout(
        title="เปรียบเทียบ KPI กับเป้าหมาย",
        barmode='group',
        legend=dict(orientation="h", yanchor="bottom", y=1.02, xanchor="right", x=1)
    )
    
    st.plotly_chart(fig, use_container_width=True)
    
    # แสดงตาราง KPI
    st.subheader("ตาราง KPI")
    
    # ฟังก์ชันสร้างสีพื้นหลังตามความสำเร็จ
    def color_success(val):
        if val >= 95:
            return 'background-color: #c6efce'  # เขียว
        elif val >= 80:
            return 'background-color: #ffeb9c'  # เหลือง
        else:
            return 'background-color: #ffc7ce'  # แดง
    
    # ใช้ Styler เพื่อจัดรูปแบบตาราง
    styled_kpi = kpi_df.style.format({
        "เป้าหมาย": "{:.2f}",
        "ปัจจุบัน": "{:.2f}",
        "ความสำเร็จ (%)": "{:.1f}%"
    }).applymap(lambda x: color_success(x), subset=["ความสำเร็จ (%)"])
    
    st.dataframe(styled_kpi, use_container_width=True)
    
    # ตั้งค่า KPI ใหม่
    st.subheader("ตั้งค่า KPI ใหม่")
    
    selected_kpi = st.selectbox("เลือก KPI", kpi_df["KPI"].tolist())
    
    # หาข้อมูล KPI ที่เลือก
    selected_kpi_data = kpi_df[kpi_df["KPI"] == selected_kpi].iloc[0]
    
    col1, col2 = st.columns(2)
    
    with col1:
        current_value = st.number_input(
            f"ค่าปัจจุบัน ({selected_kpi_data['หน่วย']})",
            value=float(selected_kpi_data["ปัจจุบัน"]),
            step=0.01
        )
    
    with col2:
        target_value = st.number_input(
            f"ค่าเป้าหมาย ({selected_kpi_data['หน่วย']})",
            value=float(selected_kpi_data["เป้าหมาย"]),
            step=0.01
        )
    
    if st.button("บันทึกการตั้งค่า KPI"):
        success_percent = (current_value / target_value * 100) if target_value > 0 else 0
        st.success(f"บันทึกการตั้งค่า KPI '{selected_kpi}' เรียบร้อยแล้ว")
        st.info(f"ความสำเร็จปัจจุบัน: {success_percent:.1f}%")
        
        # แสดงคำแนะนำในการปรับปรุง
        if success_percent < 80:
            st.warning("คำแนะนำในการปรับปรุง: KPI นี้ยังต่ำกว่าเป้าหมายมาก ควรตรวจสอบและวิเคราะห์สาเหตุโดยด่วน")
        elif success_percent < 95:
            st.info("คำแนะนำในการปรับปรุง: KPI นี้ใกล้ถึงเป้าหมาย ควรมีการปรับปรุงเล็กน้อย")
        else:
            st.success("KPI นี้เป็นไปตามเป้าหมายแล้ว ควรรักษาระดับนี้ไว้")

    # เพิ่ม KPI ใหม่
    st.subheader("เพิ่ม KPI ใหม่")
    
    col1, col2, col3 = st.columns(3)
    
    with col1:
        new_kpi_name = st.text_input("ชื่อ KPI ใหม่")
    
    with col2:
        new_kpi_unit = st.text_input("หน่วย")
    
    with col3:
        new_kpi_target = st.number_input("ค่าเป้าหมาย", min_value=0.0, step=0.1)
    
    if st.button("เพิ่ม KPI ใหม่"):
        if new_kpi_name and new_kpi_unit:
            st.success(f"เพิ่ม KPI '{new_kpi_name}' เรียบร้อยแล้ว")
        else:
            st.error("กรุณาระบุชื่อ KPI และหน่วย")

    # ตัวอย่าง KPI ที่แนะนำ
    st.subheader("ตัวอย่าง KPI ที่แนะนำสำหรับระบบไฮโดรโปนิก")
    
    recommended_kpi = {
        "KPI": [
            "คุณภาพผลผลิต (คะแนน 1-10)",
            "ระยะเวลาการเก็บเกี่ยว (วัน)",
            "อัตราการเจริญเติบโตสัปดาห์แรก (%)",
            "อัตราการสูญเสียพืช (%)",
            "ประสิทธิภาพการใช้ปุ๋ย (kg/kg)",
            "ต้นทุนต่อกิโลกรัม (บาท/kg)"
        ],
        "คำอธิบาย": [
            "วัดคุณภาพของผลผลิตด้วยการให้คะแนนจากสี ขนาด รสชาติ และลักษณะภายนอก",
            "วัดจำนวนวันตั้งแต่เริ่มปลูกจนถึงวันเก็บเกี่ยว",
            "วัดการเติบโตในสัปดาห์แรกเทียบกับค่าเป้าหมาย",
            "เปอร์เซ็นต์ของพืชที่ไม่สามารถเติบโตจนเก็บเกี่ยวได้",
            "วัดปริมาณปุ๋ยที่ใช้ต่อปริมาณผลผลิตที่ได้",
            "คำนวณต้นทุนทั้งหมดต่อปริมาณผลผลิต 1 กิโลกรัม"
        ]
    }
    
    recommended_kpi_df = pd.DataFrame(recommended_kpi)
    st.dataframe(recommended_kpi_df, use_container_width=True)

# สร้างส่วนท้าย
st.sidebar.markdown("---")
st.sidebar.info("ระบบพัฒนาโดย Hydroponics Analytics")
st.sidebar.text("เวอร์ชัน 1.0.0")