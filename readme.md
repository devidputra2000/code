# Data Science Practicals 1-10 (FINAL)

Fixed and finalized. Each practical is one complete, runnable block (for Google Colab), named as in the original question.




---

## Practical 2A: TEXT DELIMITED CSV TO HORUS FORMAT

```python
import csv
import json
from google.colab import files

# Step-01 Create a sample CSV File
csv_file = "students.csv"
students = [
    ["Id", "Name", "Age", "Course"],
    [1, "A", 20, "MSCIT"],
    [2, "B", 21, "MSCIT"],
    [3, "C", 22, "MSCIT"],
    [4, "D", 23, "MSCIT"],
    [5, "E", 24, "MSCIT"]
]
with open(csv_file, "w", newline="", encoding="utf-8") as f:
    writer = csv.writer(f)
    writer.writerows(students)
print("Sample CSV File Created:", csv_file)

# Step-02 Convert CSV to HORUS Format
def csv_to_horus(input_file, output_file):
    records = []
    with open(input_file, "r", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        for row in reader:
            records.append({
                "type": "record",
                "data": dict(row)
            })

    horus = {
        "horus_version": "1.0",
        "source_format": "CSV",
        "records": records
    }

    # Save HORUS output
    with open(output_file, "w", encoding="utf-8") as f:
        json.dump(horus, f, indent=4)
    return horus

# Step-03 Perform Conversion
output_file = "students_horus.json"
horus_data = csv_to_horus(csv_file, output_file)

# Step-04 Display HORUS Output
print("\n ======== HORUS OUTPUT ========")
print(json.dumps(horus_data, indent=4))

# Step-05 Download the HORUS File
files.download(output_file)
```

---

## Practical 2B: XML TO HORUS FORMAT

```python
import xml.etree.ElementTree as ET
import json
from google.colab import files

# STEP 1: Create a sample XML file
xml_file = "students.xml"
xml_data = """<students>
    <student>
        <id>1</id>
        <name>Rahul</name>
        <age>20</age>
        <course>BCA</course>
    </student>
    <student>
        <id>2</id>
        <name>Priya</name>
        <age>21</age>
        <course>BSc IT</course>
    </student>
    <student>
        <id>3</id>
        <name>Amit</name>
        <age>20</age>
        <course>BCA</course>
    </student>
</students>"""

with open(xml_file, "w", encoding="utf-8") as f:
    f.write(xml_data)

print("Sample XML file created:", xml_file)

# STEP 2: Convert XML elements into Python dictionary
def xml_to_dict(element):
    # If the element has no child elements
    if len(element) == 0:
        return element.text.strip() if element.text else ""

    data = {}
    for child in element:
        child_data = xml_to_dict(child)
        # Handle repeated elements
        if child.tag in data:
            if not isinstance(data[child.tag], list):
                data[child.tag] = [data[child.tag]]
            data[child.tag].append(child_data)
        else:
            data[child.tag] = child_data
    return data

# STEP 3: Convert XML to HORUS
def xml_to_horus(input_file, output_file):
    tree = ET.parse(input_file)
    root = tree.getroot()
    data = xml_to_dict(root)

    horus = {
        "horus_version": "1.0",
        "source_format": "XML",
        "root": root.tag,
        "data": data
    }

    # Save HORUS output
    with open(output_file, "w", encoding="utf-8") as f:
        json.dump(horus, f, indent=4)

    return horus

# STEP 4: Perform conversion
output_file = "students_horus.xml.json"
horus_data = xml_to_horus(xml_file, output_file)

# STEP 5: Display HORUS output
print("\n========== HORUS OUTPUT ==========\n")
print(json.dumps(horus_data, indent=4))

# STEP 6: Download HORUS file
files.download(output_file)
```

---

## Practical 2C: JSON TO HORUS

```python
import json
from google.colab import files

# STEP 1: CREATE SAMPLE JSON DATA
data = [
    {"id": 101, "Name": "Suraj",    "Department": "IT", "Marks": 85},
    {"id": 102, "Name": "Neeraj",   "Department": "IT", "Marks": 86},
    {"id": 103, "Name": "Devil",    "Department": "IT", "Marks": 90},
    {"id": 104, "Name": "Abhishek", "Department": "IT", "Marks": 80},
    {"id": 105, "Name": "Gauresh",  "Department": "IT", "Marks": 87},
]

# STEP 2: SAVE DATA IN JSON FORMAT
with open("students.json", "w") as file:
    json.dump(data, file, indent=1)

print("JSON CREATED SUCCESSFULLY")

# STEP 3: READ JSON DATA
with open("students.json", "r") as file:
    data = json.load(file)

print("\n Original JSON Data")
print(json.dumps(data, indent=4))

# STEP 4: CONVERT JSON DATA TO SAMPLE HORUS TEXT
def convert_to_horus(records):
    horus_records = []
    for record in records:
        fields = []

        for key, value in record.items():
            fields.append(f"{key} = {value}")

        horus_records.append(" | ".join(fields))
    return "\n".join(horus_records)

horus_data = convert_to_horus(data)

# STEP 5: DISPLAY CONVERTED DATA
print("\n Converted Horus Data:")
print(horus_data)

# STEP 6: SAVE OUTPUT TO A HORUS FILE
with open("students.horus", "w") as file:
    file.write(horus_data)

print("\nOutput successfully saved to 'students.horus'")

# STEP 7: DOWNLOAD THE OUTPUT FILE
files.download("students.horus")
```

---

## Practical 2D: MYSQL DATABASE TO HORUS

```python
import pandas as pd
import sqlite3 as sq
from google.colab import files

# STEP 1: CREATE A SAMPLE DATABASE
sInputFileName = "/content/utility.db"
sInputTable = "Country_Code"

conn = sq.connect(sInputFileName)

# STEP 2: CREATE AND POPULATE THE COUNTRY CODE TABLE
create_table = """
CREATE TABLE IF NOT EXISTS Country_Code(
"Country CODE" TEXT,
"ISO-2-CODE" TEXT,
"ISO-3-CODE" TEXT,
"ISO-3-CODE-ALPHA" TEXT
)
"""

conn.execute("DROP TABLE IF EXISTS Country_Code")
conn.execute(create_table)

country_data = [
    ("Afghanistan", "AF", "AFG", "AF"),
    ("Albania", "AL", "ALB", "AL"),
    ("Algeria", "DZ", "DZA", "DZ"),
    ("Andorra", "AD", "AND", "AD"),
    ("Angola", "AO", "AGO", "AO"),
]

conn.executemany("INSERT INTO Country_Code VALUES(?,?,?,?)", country_data)
conn.commit()

# STEP 3: READ DATA FROM THE DATABASE
sSQL = f"SELECT * FROM {sInputTable}"
InputData = pd.read_sql_query(sSQL, conn)

print("Input Data Values")
print(InputData)

# STEP 4: PROCESS THE DATA
ProcessData = InputData.copy()

# Remove unnecessary columns
ProcessData.drop("ISO-2-CODE", axis=1, inplace=True)
ProcessData.drop("ISO-3-CODE", axis=1, inplace=True)

# Rename columns
ProcessData.rename(
    columns={
        "Country CODE": "CountryName",
        "ISO-3-CODE-ALPHA": "CountryNumber"
    },
    inplace=True
)

# Set CountryNumber as the index
ProcessData.set_index("CountryNumber", inplace=True)

# Sort country names in descending order
ProcessData.sort_values(
    by="CountryName",
    axis=0,
    ascending=False,
    inplace=True
)

print("\n Processed Data Values")
print(ProcessData)

# STEP 5: SAVE THE CONVERTED OUTPUT
OutputData = ProcessData
sOutputFileName = "/content/HORUS-MySQL-Country.csv"
OutputData.to_csv(sOutputFileName, index=True)
print("\nDatabase to HORUS - Done")
print("Output file:", sOutputFileName)

# STEP 6: CLOSE DATABASE CONNECTION
conn.close()

# STEP 7: DOWNLOAD OUTPUT FILE
files.download(sOutputFileName)
```

---

## Practical 2E: PICTURE TO HORUS

```python
import numpy as np
from PIL import Image
from google.colab import files

# STEP 1: UPLOAD AN IMAGE
print("Upload an image file (JPG, PNG, etc.)")
uploaded = files.upload()

# STEP 2: READ THE UPLOADED IMAGE
filename = next(iter(uploaded))
image = Image.open(filename).convert("RGB")
print("\nImage loaded successfully!")
print("Image size:", image.size)
print("Image mode:", image.mode)

# STEP 3: RESIZE IMAGE FOR MANAGEABLE PROCESSING
image = image.resize((100, 100))

# STEP 4: CONVERT IMAGE INTO A NUMERICAL ARRAY
image_array = np.array(image)
print("\nImage converted into numerical data")
print("Array shape:", image_array.shape)

# STEP 5: FLATTEN IMAGE PIXEL VALUES
pixel_data = image_array.reshape(-1, 3)

# STEP 6: SAVE PIXEL DATA AS CSV FILE
output_file = "picture_horus.csv"
np.savetxt(
    output_file,
    pixel_data,
    delimiter=",",
    fmt="%d",
    header="Red,Green,Blue",
    comments=""
)

# STEP 7: DISPLAY SAMPLE OUTPUT
print("\nFirst 10 pixel values:")
print(pixel_data[:10])
print("\nPicture conversion completed!")
print("Output saved as:", output_file)

# STEP 8: DOWNLOAD OUTPUT
files.download(output_file)
```

---

## Practical 2F: VIDEO TO HORUS

```python
import os
import cv2
import shutil
import numpy as np
import pandas as pd
from google.colab import files

# STEP 1: UPLOAD VIDEO
print("Upload Your Video File")
uploaded = files.upload()
video_name = next(iter(uploaded))
video_path = "/content/" + video_name

# STEP 2: CREATE TEMPORARY FOLDER FOR FRAMES
frame_folder = "/content/temp"
if os.path.exists(frame_folder):
    shutil.rmtree(frame_folder)
os.makedirs(frame_folder)
print("\nStart Movie to Frames")

# STEP 3: OPEN VIDEO
vidcap = cv2.VideoCapture(video_path)
if not vidcap.isOpened():
    raise ValueError("Unable to open video. Please upload a valid video file.")
fps = vidcap.get(cv2.CAP_PROP_FPS)
if fps <= 0:
    fps = 30
total_frames = int(vidcap.get(cv2.CAP_PROP_FRAME_COUNT))
duration = total_frames / fps
print("Video FPS:", fps)
print("Total video frames:", total_frames)
print("Duration in seconds:", round(duration, 2))

# Extract approximately one frame per second
frame_interval = max(1, int(fps))
count = 0
frame_number = 0
while True:
    success, image = vidcap.read()
    if not success:
        break
    if frame_number % frame_interval == 0:
        frame_path = os.path.join(
            frame_folder,
            f"frame-{count:04d}.jpg"
        )
        cv2.imwrite(frame_path, image)
        count += 1
    frame_number += 1
vidcap.release()
print("\nGenerated", count, "frames")
print("Movie to Frames - Done")

# STEP 4: CONVERT EXTRACTED FRAMES TO HORUS DATA
print("\nStart Frames to HORUS")
records = []
# Resize each frame to keep output manageable
for frame_file in sorted(os.listdir(frame_folder)):
    if not frame_file.lower().endswith((".jpg", ".jpeg", ".png")):
        continue
    frame_path = os.path.join(frame_folder, frame_file)
    image = cv2.imread(frame_path)
    if image is None:
        continue
    # Resize image
    image = cv2.resize(image, (32, 32))
    # Convert BGR to RGB
    image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
    height, width, channels = image.shape
    # Read every pixel
    for y in range(height):
        for x in range(width):
            red, green, blue = image[y, x]
            records.append({
                "FrameName": frame_file,
                "XAxis": x,
                "YAxis": y,
                "Red": int(red),
                "Green": int(green),
                "Blue": int(blue),
                "Alpha": 255
            })

# STEP 5: CREATE HORUS DATABASE
columns = [
    "XAxis", "YAxis", "Red", "Green",
    "Blue", "Alpha", "FrameName"
]
ProcessData = pd.DataFrame(records, columns=columns)
print("\nHORUS Data Preview:")
print(ProcessData.head(10))
print("\nRows", ProcessData.shape[0])
print("\nColumns", ProcessData.shape[1])

# STEP 6: SAVE HORUS OUTPUT TO CSV
output_file = "/content/HORUS-Movie.csv"
ProcessData.to_csv(output_file, index=False)
print("\nMovie to HORUS - Done")
print("Output saved as:", output_file)

# STEP 7: DOWNLOAD OUTPUT FILE
files.download(output_file)
```

---

## Practical 2G: AUDIO TO HORUS

```python
import os
import subprocess
import numpy as np
import pandas as pd
from scipy.io import wavfile
from scipy.signal import resample_poly
from google.colab import files

# STEP 1: INSTALL FFMPEG IF REQUIRED
print("Checking audio conversion tool...")
if (
    subprocess.run(
        ["bash", "-lc", "command -v ffmpeg"],
        capture_output=True,
    ).returncode
    != 0
):
    subprocess.run(["apt-get", "update", "-qq"], check=True)
    subprocess.run(["apt-get", "install", "-qq", "ffmpeg"], check=True)

# STEP 2: UPLOAD AUDIO FILE
print("\nUpload your audio file (MP3 or WAV)")
uploaded = files.upload()
if not uploaded:
    raise ValueError("No audio file was uploaded.")
audio_file = next(iter(uploaded))

# STEP 3: CONVERT UPLOADED AUDIO TO WAV
wav_file = "/content/converted_audio.wav"
result = subprocess.run(
    [
        "ffmpeg", "-y", "-i", audio_file,
        "-vn", "-ac", "1", "-ar", "16000",
        wav_file
    ],
    capture_output=True,
    text=True
)
if result.returncode != 0:
    raise ValueError(
        "Audio conversion failed. Please check:\n"
        + result.stderr[-1500:]
    )
print("\nAudio converted to WAV successfully!")

# STEP 4: READ THE WAV FILE
sample_rate, audio_data = wavfile.read(wav_file)
print("Sample rate:", sample_rate, "Hz")
print("Audio samples:", len(audio_data))

# STEP 5: CONVERT SAMPLES TO NORMALIZED NUMERICAL VALUES
audio_data = audio_data.astype(np.float64)
maximum = np.max(np.abs(audio_data)) if len(audio_data) else 0
if maximum > 0:
    audio_data = audio_data / maximum

# STEP 6: CONVERT SAMPLES TO MANAGEABLE OUTPUT
target_rate = 1000
if sample_rate > target_rate:
    audio_data = resample_poly(
        audio_data, target_rate, sample_rate
    )
    effective_rate = target_rate
else:
    effective_rate = sample_rate

# STEP 7: CREATE HORUS-STYLE TABULAR FORMAT DATA
ProcessData = pd.DataFrame({
    "Time": np.arange(len(audio_data)) / effective_rate,
    "Amplitude": audio_data
})
print("\nAudio Data Preview:")
print(ProcessData.head(10))

# STEP 8: SAVE OUTPUT
output_file = "/content/HORUS-Audio.csv"
ProcessData.to_csv(output_file, index=False)
print("\nAudio to HORUS - Done")
print("Output file:", output_file)
print("Total records:", len(ProcessData))

# STEP 9: DOWNLOAD OUTPUT
files.download(output_file)
```

---

## Practical 5A: ERROR MANAGEMENT

```python
import pandas as pd

data_suraj = {
    "Name": ['John', 'Alice', None, 'John'],
    "Age": [23, 24, None, 26],
    "Salary": [5000, 6000, None, 7000]
}

df_suraj = pd.DataFrame(data_suraj)

print("Original Data")
print(df_suraj)

df_suraj = df_suraj.drop_duplicates()

df_suraj["Age"] = df_suraj["Age"].fillna(df_suraj["Age"].mean())
df_suraj["Salary"] = df_suraj["Salary"].fillna(df_suraj["Salary"].mean())
df_suraj["Name"] = df_suraj["Name"].fillna("Unknown")

print("Cleaned Data")
print(df_suraj)
```

---

## Practical 5B: NETWORK ROUTING DIAGRAM

```python
import networkx as nx
import matplotlib.pyplot as plt

G = nx.Graph()
G.add_edges_from([
    (1, 2), (1, 3), (2, 4), (3, 5)
])

print("Is DAG?", nx.is_directed_acyclic_graph(G))
nx.draw(G, with_labels=True,
        node_size=2000,
        node_color="LightBlue")
plt.show()
```

---

## Practical 5C: BUILD ACYCLIC GRAPH

> **Note:** Fixed: the original edge ('R4','R1') created a cycle (R1→R2→R4→R1), so the graph was not acyclic. It is now ('R1','R4').

```python
import networkx as nx
import matplotlib.pyplot as plt

G = nx.DiGraph()
G.add_edges_from([
    ('R1', 'R2'), ('R3', 'R4'), ('R1', 'R4'), ('R2', 'R4')
])

print("Is DAG ?", nx.is_directed_acyclic_graph(G))
nx.draw(G, with_labels=True,
        node_color="Blue",
        font_color="White",
        node_size=2000)
plt.show()
```

---

## Practical 5D: PICK CONTENT FOR BILLBOARD FROM GIVEN DATA

```python
import pandas as pd

df = pd.DataFrame({
    "Products": ["Phone", "TV", "Tablet", "Laptop"],
    "Sales": [120, 130, 150, 140]
})

billboard = df.sort_values("Sales", ascending=False)
print(billboard)

print("\nBillboard Content (Top Seller):", billboard.iloc[0]["Products"])
```

---

## Practical 5E: GML FILE FROM CSV FILE

```python
import pandas as pd
import networkx as nx

data = {
    "Sources": ['A', 'B', 'C'],
    "Targets": ['B', 'C', 'D']
}

df = pd.DataFrame(data)
G_gml = nx.from_pandas_edgelist(df, source="Sources", target="Targets")
nx.write_gml(G_gml, "network.gml")
print("GML FILE CREATED")
```

---

## Practical 5F: PLAN LOCATION OF WAREHOUSE FROM GIVEN DATA

```python
import pandas as pd

df_ware = pd.DataFrame({
    "X": [10, 20, 30, 40],
    "Y": [15, 25, 35, 45]
})

warehouse_x = df_ware["X"].mean()
warehouse_y = df_ware["Y"].mean()
print("Suggested Warehouse Location")
print(warehouse_x, warehouse_y)
```

---

## Practical 5G: LOCATION OF WAREHOUSE USING CLUSTERING

> **Note:** Fixed: the original never called plt.show(), and the clustering fit included the Cluster column on later runs; it now fits on X and Y only.

```python
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans

df_cluster = pd.DataFrame({
    "X": [10, 20, 30, 40, 50],
    "Y": [15, 25, 35, 45, 55]
})

kmeans = KMeans(n_clusters=3, random_state=0, n_init=10)
df_cluster["Cluster"] = kmeans.fit_predict(df_cluster[["X", "Y"]])
print(df_cluster)

plt.scatter(df_cluster["X"], df_cluster["Y"],
            c=df_cluster["Cluster"],
            s=100)

plt.scatter(kmeans.cluster_centers_[:, 0],
            kmeans.cluster_centers_[:, 1],
            marker="X",
            s=200,
            color="red")

plt.xlabel("X")
plt.ylabel("Y")
plt.title("Warehouse Locations using KMeans Clustering")
plt.show()
```

---

## Practical 5H: SHIPPING ROUTES FROM BEST FIT INTERNATIONAL LOGISTICS

```python
import networkx as nx

G = nx.Graph()
G.add_weighted_edges_from([
    ('Mumbai', 'Delhi', 5),
    ('Delhi', 'Chennai', 7),
    ('Mumbai', 'Chennai', 15)
])

route = nx.shortest_path(G, 'Mumbai', 'Chennai', weight="weight")
print(route)
```

---

## Practical 5I: TO DELETE THE BEST PACKING OPTIONS TO SHIPPING CONTAINERS FROM THE GIVEN DATA

```python
import pandas as pd

df = pd.DataFrame({
    "Packing": ["Box A", "Box B", "Box C"],
    "Cost": [500, 400, 300]
})

best = df["Cost"].idxmin()
df = df.drop(best)
print(df)
```

---

## Practical 5J: TO CREATE BEST DELIVERY ROUTE

> **Note:** Fixed: edges are now given as an ordered list (a set has no order), and the route itself is printed.

```python
import networkx as nx
import matplotlib.pyplot as plt

G = nx.DiGraph()
G.add_edges_from([
    ("Warehouse", "Area1"),
    ("Area1", "Area2"),
    ("Area2", "Customer")
])

route = nx.shortest_path(G, "Warehouse", "Customer")
print("Best Delivery Route:", route)

nx.draw(G, with_labels=True,
        arrows=True,
        node_size=2500,
        node_color="LightGreen")
plt.show()
```

---

## Practical 5K: FOREX TRADING PLANNER

```python
import pandas as pd

df = pd.DataFrame({
    "Rate": [82.5, 83.0, 82.8, 84.1]
})

buy = df["Rate"].min()
sell = df["Rate"].max()
print("Buy At:", buy)
print("Sell At:", sell)

profit = sell - buy
print("Profit:", profit)
```

---

## Practical 5L: BALANCE SHEET TO ENSURE ONLY GOOD DATA IS PROCESSING

```python
import pandas as pd

df = pd.DataFrame({
    "Revenue": [10000, 20000, 30000],
    "Expense": [6000, 5000, None]
})

df = df.fillna(0)
df["Profit"] = df["Revenue"] - df["Expense"]
print(df)
```

---

## Practical 5M: GENERATE PAYROLL FROM THE GIVEN DATA

```python
import pandas as pd

df = pd.DataFrame({
    "Employee": ["John", "Alice", "Bob"],
    "Basic": [3000, 4000, 5000]
})

df["HRA"] = df["Basic"] * 0.20
df["DA"] = df["Basic"] * 0.10
df["Gross Salary"] = df["Basic"] + df["HRA"] + df["DA"]

print(df)
```

---

## Practical 6: BUILD HUB, LINKS AND SATELLITE

> **Note:** Fixed: the original printed an undefined df (now df_s); the 'Load Data' column is now 'Load_Date' and is included in the Satellite.

```python
import pandas as pd
import matplotlib.pyplot as plt
import networkx as nx

data = {
    "CustomerID": [101, 102, 103, 104],
    "OrderID": [1, 2, 3, 4],
    "CustomerName": ['A', 'B', 'C', 'D'],
    "Location": ['D', 'E', 'F', 'G'],
    "Amount": [1000, 2000, 3000, 4000],
    "Load_Date": pd.Timestamp.today().date()
}

df_s = pd.DataFrame(data)
print(df_s)

# Hub
hub_customer = df_s[["CustomerID"]].drop_duplicates()
print("\nHub - Customer")
print(hub_customer)

# Link
link_customer = df_s[["CustomerID", "OrderID"]].drop_duplicates()
print("\nLink - Customer/Order")
print(link_customer)

# Satellite
satellite_customer = df_s[["CustomerID", "OrderID", "CustomerName", "Location", "Amount", "Load_Date"]]
print("\nSatellite - Customer")
print(satellite_customer)

# Chart
plt.bar(satellite_customer['CustomerName'], satellite_customer['Amount'])
plt.xlabel("Customer Name")
plt.ylabel("Amount")
plt.show()
```

---

## Practical 7: TRANSFORMING DATA

> **Note:** Requires /content/titanic.csv (upload it to Colab first). Fixed: missing values are filled once (the duplicate inplace fill was removed), and Fare 0 is no longer left out of the Low category.

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

df_suraj = pd.read_csv("/content/titanic.csv")
print(df_suraj.head())

# Remove duplicates
df = df_suraj.drop_duplicates().copy()

# Handle missing values
df["Age"] = df["Age"].fillna(df["Age"].mean())
df["Embarked"] = df["Embarked"].fillna(df["Embarked"].mode()[0])

# Check missing values remaining
print(df[["Age", "Embarked"]].isnull().sum())

# Create Age Group
df["AgeGroup"] = pd.cut(
    df["Age"],
    bins=[0, 12, 18, 35, 60, 100],
    labels=["Child", "Teen", "Adult", "Middle Age", "Senior"],
)

# Create Family Size
df["FamilySize"] = df["SibSp"] + df["Parch"] + 1

# Create Fare Category
df["FareCategory"] = pd.cut(
    df["Fare"],
    bins=[0, 100, 200, 300, 520],
    labels=["Low", "Medium", "High", "Very High"],
    include_lowest=True,
)

# Create IsAlone
df["IsAlone"] = np.where(df["FamilySize"] == 1, "Yes", "No")

print(df.head())

# Survival rate by passenger class
survival = df.groupby("Pclass")["Survived"].mean() * 100
plt.figure(figsize=(8, 6))
survival.plot(kind="bar")
plt.xlabel("Passenger Class")
plt.ylabel("Survival Rate (%)")
plt.title("Survival Rate by Passenger Class")
plt.grid(True)
plt.show()
```

---

## Practical 8: ORGANIZATION OF DATA

> **Note:** Requires /content/Social_Network_Ads.csv (upload it to Colab first). Fixed: Gender is kept as text and encoded once into Gender_Code (the old code overwrote it with 1/0 and then tried to encode again); deprecated inplace fills removed; age 18 is no longer dropped from the age groups; duplicate rename step merged; 'Outliners' spelled 'Outliers'.

```python
import pandas as pd
import numpy as np

df_suraj = pd.read_csv("/content/Social_Network_Ads.csv")
print('Original shape: \n', df_suraj.shape)
print(df_suraj.head())

# Data inspection
print("\n Information \n")
print(df_suraj.info())

print("\n Missing values \n")
print(df_suraj.isna().sum())

print("\n Duplicate values \n")
print(df_suraj.duplicated().sum())

# Remove the duplicate records
df_suraj.drop_duplicates(inplace=True)
print('Shape after removing duplicates: \n', df_suraj.shape)

# Handle missing values
for col in df_suraj.columns:
    if df_suraj[col].isna().sum() > 0:
        if pd.api.types.is_numeric_dtype(df_suraj[col]):
            df_suraj[col] = df_suraj[col].fillna(df_suraj[col].mean())
        else:
            df_suraj[col] = df_suraj[col].fillna(df_suraj[col].mode()[0])

print("\n Missing values \n")
print(df_suraj.isna().sum())

# Standardize Gender values
df_suraj['Gender'] = df_suraj['Gender'].str.strip().str.title()
print(df_suraj.head())

# Rename columns
df_suraj.rename(columns={'EstimatedSalary': 'Salary', 'Purchased': 'Purchase_Status'}, inplace=True)

# Remove impossible values
df_suraj = df_suraj[
    (df_suraj['Age'] >= 18) &
    (df_suraj['Age'] <= 65) &
    (df_suraj['Salary'] > 0)
].copy()

print("Current columns:", df_suraj.columns.tolist())
print(df_suraj.head())

# Salary bands using quantiles
df_suraj['Salary_Band'] = pd.qcut(df_suraj['Salary'], q=4, labels=["Low", "Medium", "High", "Very High"])
print(df_suraj.head())

# Age groups
bins = [18, 30, 45, 65]
labels = ['Young', 'Adult', 'Senior']
df_suraj['Age_Group'] = pd.cut(df_suraj['Age'], bins=bins, labels=labels, include_lowest=True)
print(df_suraj.head())

# Encode Gender
df_suraj['Gender_Code'] = df_suraj['Gender'].map({'Male': 1, 'Female': 0})
print(df_suraj.head())

# Normalize Salary
df_suraj['Salary_Normalized'] = (df_suraj['Salary'] - df_suraj['Salary'].min()) / (df_suraj['Salary'].max() - df_suraj['Salary'].min())
print(df_suraj.head())

# Salary Rank
df_suraj['Salary_Rank'] = df_suraj['Salary'].rank(ascending=False, method="dense")
print(df_suraj.head())

# Multi-level Sorting
df_suraj.sort_values(by=['Age_Group', 'Salary', 'Purchase_Status'], ascending=[True, False, False], inplace=True)
print(df_suraj.head())

# Group Statistics
summary = df_suraj.groupby(['Gender', 'Age_Group'], observed=False).agg(
    Total_Customers=("User ID", "count"),
    Average_Salary=("Salary", "mean"),
    Total_Salary=("Salary", "sum"),
    Max_Salary=("Salary", "max"),
    Min_Salary=("Salary", "min")
)

print("\n Group Summary")
print(summary)

# Pivot Table
pivot = pd.pivot_table(
    df_suraj,
    values='Salary',
    index='Age_Group',
    columns='Gender',
    aggfunc=['mean', 'max', 'min'],
    observed=False
)

print("\n Pivot Table")
print(pivot, "\n\n")

# MultiIndex Dataset
organized = df_suraj.set_index(["Gender", "Age_Group"])
print("\n Organized Dataset \n")
print(organized.head(), "\n\n")

# Detect Outliers
Q1 = df_suraj['Salary'].quantile(0.25)
Q3 = df_suraj['Salary'].quantile(0.75)
IQR = Q3 - Q1
Outliers = df_suraj[(df_suraj['Salary'] < Q1 - 1.5 * IQR) | (df_suraj['Salary'] > Q3 + 1.5 * IQR)]
print("\n Outliers \n")
print(Outliers)

# Export files
organized.to_csv("organized_data.csv")
summary.to_csv("group_summary.csv")
pivot.to_csv("pivot_table.csv")
Outliers.to_csv("outliers.csv")
print("\n All files created successfully in /content/ \n")
```

---

## Practical 9: GENERATING DATA

> **Note:** Fixed: n_ds = 1000 (was a syntax error); user_Id now holds real IDs 1001–2000 (was the same number in every row); salary is no longer clipped to 20,000–25,000; 'banglore' → 'Bangalore'; file path corrected to /content/.

```python
!pip install Faker

# Data Generation using Python
import pandas as pd
import numpy as np
from faker import Faker
import random

# Initialize Faker
fake = Faker()
Faker.seed(42)
random.seed(42)
np.random.seed(42)

# Number of Records
n_ds = 1000

# Generate user ID
user_Id = np.arange(1001, 1001 + n_ds)

# Generate Personal Information
gender = np.random.choice(['Male', 'Female'], size=n_ds, p=[0.55, 0.45])
age = np.random.randint(18, 61, size=n_ds)

# Generate Salary
salary = (age * 3500 + np.random.randint(15000, 80000, size=n_ds)).astype(int)
salary = np.clip(salary, 20000, 300000)

# Experience years
Exp = np.maximum(
    age - 22 + np.random.randint(-2, 4, size=n_ds),
    0
)

# Generate Education
Edu = np.random.choice(
    ['Diploma', 'Bachelor', 'Masters', 'PhD'],
    size=n_ds,
    p=[0.15, 0.50, 0.25, 0.10]
)

# Generate City
city = np.random.choice(
    ['Mumbai', 'Delhi', 'Bangalore', 'Hyderabad', 'Ahmedabad', 'Chennai', 'Kolkata', 'Pune', 'Jaipur', 'Surat'],
    size=n_ds
)

# Generate Joining Dates
join_date = [
    fake.date_between(start_date='-10y', end_date='today')
    for _ in range(n_ds)
]

# Purchase Probability
probability = (
    (salary / salary.max()) * 0.45
    + (Exp / Exp.max()) * 0.35
    + np.random.random(size=n_ds) * 0.20
)
purchased = (probability >= 0.55).astype(int)

# Build Data Frame
df = pd.DataFrame({
    'user_Id': user_Id,
    'gender': gender,
    'Age': age,
    'Salary': salary,
    'Education': Edu,
    'City': city,
    'Joining Date': join_date,
    'Purchased': purchased
})

# Introducing Missing Values
for column in ["Age", "Salary", "Education"]:
    missing_rows = np.random.choice(
        df.index,
        15,
        replace=False
    )
    df.loc[missing_rows, column] = np.nan

# Introduce Duplicate Records
duplicates = df.sample(20, random_state=42)
df = pd.concat(
    [df, duplicates],
    ignore_index=True
)

# Shuffle Dataset
df = df.sample(
    frac=1,
    random_state=42
).reset_index(
    drop=True)

# Display the dataset
print("\n Dataset Shape")
print(df.shape)
print("\n Data Types")
print(df.dtypes)
print("\n First Five Rows")
print(df.head())
print("\n Last Five Rows")
print(df.tail())
print("\n Missing Values")
print(df.isnull().sum())
print("\n Statistical Summary")
print(df.describe(include='all'))

# Save Dataset
df.to_csv('Generated_Employee_Dataset.csv', index=False)
print("\n Dataset Generated Successfully")
print("File Name : Generated_Employee_Dataset.csv")
print("File Location : /content/Generated_Employee_Dataset.csv")
```
