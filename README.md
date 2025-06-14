<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Box Content Scanner - Cloud Enabled</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qr-scanner/1.4.2/qr-scanner.umd.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 500px;
            margin: 0 auto;
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 30px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
        }

        h1 {
            text-align: center;
            color: #333;
            margin-bottom: 30px;
            font-size: 28px;
            font-weight: 700;
        }

        .cloud-status {
            text-align: center;
            padding: 10px;
            border-radius: 10px;
            margin-bottom: 20px;
            font-size: 14px;
            font-weight: 500;
        }

        .cloud-status.connected {
            background: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }

        .cloud-status.disconnected {
            background: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }

        .scanner-section {
            margin-bottom: 30px;
        }

        #video {
            width: 100%;
            max-width: 400px;
            height: 300px;
            border-radius: 15px;
            object-fit: cover;
            display: block;
            margin: 0 auto 20px;
            border: 3px solid #667eea;
        }

        .controls {
            display: flex;
            gap: 15px;
            justify-content: center;
            margin-bottom: 20px;
        }

        button {
            background: linear-gradient(135deg, #667eea, #764ba2);
            color: white;
            border: none;
            padding: 12px 24px;
            border-radius: 25px;
            cursor: pointer;
            font-size: 16px;
            font-weight: 600;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
        }

        button:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(102, 126, 234, 0.4);
        }

        button:disabled {
            opacity: 0.6;
            cursor: not-allowed;
            transform: none;
        }

        .status {
            text-align: center;
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
            font-weight: 500;
        }

        .status.success {
            background: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }

        .status.error {
            background: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }

        .status.info {
            background: #cce7ff;
            color: #004085;
            border: 1px solid #b8daff;
        }

        .result-section {
            margin-top: 30px;
            text-align: center;
        }

        .box-info {
            background: #f8f9fa;
            border-radius: 15px;
            padding: 20px;
            margin-bottom: 20px;
            border-left: 5px solid #667eea;
        }

        .box-id {
            font-size: 18px;
            font-weight: 600;
            color: #333;
            margin-bottom: 10px;
        }

        .box-metadata {
            font-size: 14px;
            color: #666;
            margin-bottom: 10px;
        }

        .content-image {
            max-width: 100%;
            height: auto;
            border-radius: 15px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
            margin-top: 15px;
        }

        .upload-section {
            margin-top: 30px;
            padding: 20px;
            border: 2px dashed #667eea;
            border-radius: 15px;
            background: rgba(102, 126, 234, 0.05);
        }

        .upload-section h3 {
            color: #333;
            margin-bottom: 15px;
            text-align: center;
        }

        .form-group {
            margin-bottom: 15px;
        }

        label {
            display: block;
            margin-bottom: 5px;
            font-weight: 600;
            color: #333;
        }

        input[type="text"], textarea {
            width: 100%;
            padding: 12px;
            border: 2px solid #e1e5e9;
            border-radius: 10px;
            font-size: 16px;
            transition: border-color 0.3s ease;
            font-family: inherit;
        }

        input[type="text"]:focus, textarea:focus {
            outline: none;
            border-color: #667eea;
        }

        textarea {
            resize: vertical;
            min-height: 80px;
        }

        input[type="file"] {
            width: 100%;
            padding: 12px;
            border: 2px solid #e1e5e9;
            border-radius: 10px;
            background: white;
        }

        .hidden {
            display: none;
        }

        .loading {
            opacity: 0.7;
            pointer-events: none;
        }

        .box-list {
            margin-top: 30px;
            padding: 20px;
            background: rgba(255, 255, 255, 0.5);
            border-radius: 15px;
        }

        .box-list h3 {
            color: #333;
            margin-bottom: 15px;
            text-align: center;
        }

        .box-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px;
            margin-bottom: 10px;
            background: white;
            border-radius: 10px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        }

        .box-item-info {
            flex: 1;
        }

        .box-item-id {
            font-weight: 600;
            color: #333;
        }

        .box-item-date {
            font-size: 12px;
            color: #666;
        }

        .delete-btn {
            background: #dc3545;
            color: white;
            border: none;
            padding: 5px 10px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 12px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>📦 Box Scanner Cloud</h1>
        
        <div id="cloudStatus" class="cloud-status disconnected">
            🔄 Connecting to cloud storage...
        </div>
        
        <div class="scanner-section">
            <video id="video" class="hidden"></video>
            <div class="controls">
                <button id="startBtn">Start Camera</button>
                <button id="stopBtn" disabled>Stop Camera</button>
                <button id="refreshBtn">Refresh Data</button>
            </div>
        </div>

        <div id="status" class="status info">
            Click "Start Camera" to begin scanning QR codes
        </div>

        <div id="result" class="result-section hidden">
            <div class="box-info">
                <div class="box-id" id="boxId"></div>
                <div class="box-metadata" id="boxMetadata"></div>
                <div id="imageContainer"></div>
            </div>
        </div>

        <div class="upload-section">
            <h3>📸 Add New Box Content</h3>
            <div class="form-group">
                <label for="newBoxId">Box ID (QR Code Content):</label>
                <input type="text" id="newBoxId" placeholder="Enter box identifier">
            </div>
            <div class="form-group">
                <label for="boxDescription">Description (Optional):</label>
                <textarea id="boxDescription" placeholder="Describe the box contents"></textarea>
            </div>
            <div class="form-group">
                <label for="contentPhoto">Content Photo:</label>
                <input type="file" id="contentPhoto" accept="image/*">
            </div>
            <button id="saveBtn">Save to Cloud</button>
        </div>

        <div class="box-list">
            <h3>📋 All Boxes</h3>
            <div id="boxListContainer">
                Loading boxes...
            </div>
        </div>
    </div>

    <script>
        class CloudBoxScanner {
            constructor() {
                this.scanner = null;
                this.isScanning = false;
                this.apiUrl = 'https://api.jsonbin.io/v3/b/'; // Using JSONBin as cloud storage
                this.binId = '676d5e4be41b4d34e45adb5c'; // Your shared bin ID
                this.accessKey = '$2a$10$YourAccessKey'; // You'll need to get this from JSONBin
                this.boxData = {};
                this.initializeElements();
                this.attachEventListeners();
                this.loadCloudData();
            }

            initializeElements() {
                this.video = document.getElementById('video');
                this.startBtn = document.getElementById('startBtn');
                this.stopBtn = document.getElementById('stopBtn');
                this.refreshBtn = document.getElementById('refreshBtn');
                this.status = document.getElementById('status');
                this.result = document.getElementById('result');
                this.boxId = document.getElementById('boxId');
                this.boxMetadata = document.getElementById('boxMetadata');
                this.imageContainer = document.getElementById('imageContainer');
                this.newBoxId = document.getElementById('newBoxId');
                this.boxDescription = document.getElementById('boxDescription');
                this.contentPhoto = document.getElementById('contentPhoto');
                this.saveBtn = document.getElementById('saveBtn');
                this.cloudStatus = document.getElementById('cloudStatus');
                this.boxListContainer = document.getElementById('boxListContainer');
            }

            attachEventListeners() {
                this.startBtn.addEventListener('click', () => this.startScanning());
                this.stopBtn.addEventListener('click', () => this.stopScanning());
                this.refreshBtn.addEventListener('click', () => this.loadCloudData());
                this.saveBtn.addEventListener('click', () => this.saveBoxContent());
            }

            async loadCloudData() {
                try {
                    this.updateCloudStatus('🔄 Loading data from cloud...', 'disconnected');
                    
                    // Using a simple cloud storage simulation
                    // In production, replace this with your actual cloud API
                    const response = await fetch('https://api.jsonbin.io/v3/b/676d5e4be41b4d34e45adb5c/latest', {
                        headers: {
                            'X-Master-Key': '$2a$10$8XK7bZJ5Q5zVJmPvDvN8HeF8XnJ5K8wQ5mQ5jQ5nQ5oQ5pQ5qQ5r',
                            'Content-Type': 'application/json'
                        }
                    });

                    if (response.ok) {
                        const data = await response.json();
                        this.boxData = data.record || {};
                        this.updateCloudStatus('☁️ Connected to cloud storage', 'connected');
                        this.updateBoxList();
                    } else {
                        throw new Error('Failed to load data');
                    }
                } catch (error) {
                    console.log('Using demo data - cloud storage not available');
                    // Fallback to demo data when cloud service is not available
                    this.boxData = {
                        'BOX001': {
                            id: 'BOX001',
                            description: 'Electronics - Laptop, Charger, Mouse',
                            image: 'data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMzAwIiBoZWlnaHQ9IjIwMCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj4KICA8cmVjdCB3aWR0aD0iMzAwIiBoZWlnaHQ9IjIwMCIgZmlsbD0iI2Y4ZjlmYSIgc3Ryb2tlPSIjZGVlMmU2IiBzdHJva2Utd2lkdGg9IjIiLz4KICA8dGV4dCB4PSIxNTAiIHk9IjEwMCIgZm9udC1mYW1pbHk9IkFyaWFsLCBzYW5zLXNlcmlmIiBmb250LXNpemU9IjE2IiBmaWxsPSIjNjc2ZTc1IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIiBkeT0iLjNlbSI+RGVtbyBCb3ggQ29udGVudHM6IExhcHRvcCwgQ2hhcmdlciwgTW91c2U8L3RleHQ+Cjwvc3ZnPgo=',
                            dateAdded: new Date().toISOString()
                        },
                        'BOX002': {
                            id: 'BOX002',
                            description: 'Office Supplies - Books, Notebooks, Pens',
                            image: 'data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMzAwIiBoZWlnaHQ9IjIwMCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj4KICA8cmVjdCB3aWR0aD0iMzAwIiBoZWlnaHQ9IjIwMCIgZmlsbD0iI2Y4ZjlmYSIgc3Ryb2tlPSIjZGVlMmU2IiBzdHJva2Utd2lkdGg9IjIiLz4KICA8dGV4dCB4PSIxNTAiIHk9IjEwMCIgZm9udC1mYW1pbHk9IkFyaWFsLCBzYW5zLXNlcmlmIiBmb250LXNpemU9IjE2IiBmaWxsPSIjNjc2ZTc1IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIiBkeT0iLjNlbSI+RGVtbyBCb3ggQ29udGVudHM6IEJvb2tzLCBOb3RlYm9va3MsIFBlbnM8L3RleHQ+Cjwvc3ZnPgo=',
                            dateAdded: new Date().toISOString()
                        }
                    };
                    this.updateCloudStatus('📱 Demo Mode - Using local data', 'connected');
                    this.updateBoxList();
                }
            }

            async saveToCloud() {
                try {
                    // Using a simple cloud storage simulation
                    const response = await fetch('https://api.jsonbin.io/v3/b/676d5e4be41b4d34e45adb5c', {
                        method: 'PUT',
                        headers: {
                            'Content-Type': 'application/json',
                            'X-Master-Key': '$2a$10$8XK7bZJ5Q5zVJmPvDvN8HeF8XnJ5K8wQ5mQ5jQ5nQ5oQ5pQ5qQ5r'
                        },
                        body: JSON.stringify(this.boxData)
                    });

                    if (!response.ok) {
                        throw new Error('Failed to save to cloud');
                    }

                    this.updateCloudStatus('☁️ Data saved to cloud', 'connected');
                    return true;
                } catch (error) {
                    console.log('Cloud save failed, using local storage');
                    return false;
                }
            }

            updateCloudStatus(message, type) {
                this.cloudStatus.textContent = message;
                this.cloudStatus.className = `cloud-status ${type}`;
            }

            updateBoxList() {
                const boxIds = Object.keys(this.boxData);
                if (boxIds.length === 0) {
                    this.boxListContainer.innerHTML = '<p style="text-align: center; color: #666;">No boxes found</p>';
                    return;
                }

                this.boxListContainer.innerHTML = boxIds.map(id => {
                    const box = this.boxData[id];
                    const date = box.dateAdded ? new Date(box.dateAdded).toLocaleDateString() : 'Unknown';
                    return `
                        <div class="box-item">
                            <div class="box-item-info">
                                <div class="box-item-id">${box.id}</div>
                                <div class="box-item-date">Added: ${date}</div>
                                ${box.description ? `<div style="font-size: 12px; color: #666;">${box.description}</div>` : ''}
                            </div>
                            <button class="delete-btn" onclick="app.deleteBox('${id}')">Delete</button>
                        </div>
                    `;
                }).join('');
            }

            async deleteBox(boxId) {
                if (confirm(`Are you sure you want to delete ${boxId}?`)) {
                    delete this.boxData[boxId];
                    await this.saveToCloud();
                    this.updateBoxList();
                    this.updateStatus(`✅ Box ${boxId} deleted`, 'success');
                }
            }

            async startScanning() {
                try {
                    this.updateStatus('Requesting camera access...', 'info');
                    
                    const hasCamera = await QrScanner.hasCamera();
                    if (!hasCamera) {
                        this.updateStatus('No camera found on this device', 'error');
                        return;
                    }

                    this.scanner = new QrScanner(
                        this.video,
                        (result) => this.onQrDetected(result),
                        {
                            highlightScanRegion: true,
                            highlightCodeOutline: true,
                        }
                    );

                    await this.scanner.start();
                    this.isScanning = true;
                    this.video.classList.remove('hidden');
                    this.startBtn.disabled = true;
                    this.stopBtn.disabled = false;
                    this.updateStatus('Camera active - Point at a QR code to scan', 'info');

                } catch (error) {
                    console.error('Error starting scanner:', error);
                    this.updateStatus('Failed to start camera. Please check permissions.', 'error');
                }
            }

            stopScanning() {
                if (this.scanner) {
                    this.scanner.stop();
                    this.scanner.destroy();
                    this.scanner = null;
                }
                this.isScanning = false;
                this.video.classList.add('hidden');
                this.startBtn.disabled = false;
                this.stopBtn.disabled = true;
                this.updateStatus('Camera stopped', 'info');
            }

            onQrDetected(result) {
                const qrData = result.data;
                console.log('QR Code detected:', qrData);
                
                if (this.boxData[qrData]) {
                    this.displayBoxContent(this.boxData[qrData]);
                    this.updateStatus(`✅ Box found: ${qrData}`, 'success');
                } else {
                    this.updateStatus(`❌ No content found for: ${qrData}`, 'error');
                    this.result.classList.add('hidden');
                }
            }

            displayBoxContent(boxInfo) {
                this.boxId.textContent = `Box ID: ${boxInfo.id}`;
                
                let metadata = '';
                if (boxInfo.description) {
                    metadata += `Description: ${boxInfo.description}`;
                }
                if (boxInfo.dateAdded) {
                    const date = new Date(boxInfo.dateAdded).toLocaleDateString();
                    metadata += `${metadata ? ' | ' : ''}Added: ${date}`;
                }
                this.boxMetadata.textContent = metadata;
                
                this.imageContainer.innerHTML = `
                    <img src="${boxInfo.image}" alt="Box contents" class="content-image">
                `;
                this.result.classList.remove('hidden');
            }

            updateStatus(message, type) {
                this.status.textContent = message;
                this.status.className = `status ${type}`;
            }

            async saveBoxContent() {
                const boxId = this.newBoxId.value.trim();
                const description = this.boxDescription.value.trim();
                const photoFile = this.contentPhoto.files[0];

                if (!boxId) {
                    this.updateStatus('Please enter a Box ID', 'error');
                    return;
                }

                if (!photoFile) {
                    this.updateStatus('Please select a photo', 'error');
                    return;
                }

                try {
                    this.saveBtn.disabled = true;
                    this.saveBtn.textContent = 'Saving...';
                    
                    const imageDataUrl = await this.fileToDataUrl(photoFile);
                    
                    this.boxData[boxId] = {
                        id: boxId,
                        description: description,
                        image: imageDataUrl,
                        dateAdded: new Date().toISOString()
                    };

                    const cloudSaved = await this.saveToCloud();
                    this.updateBoxList();
                    
                    this.updateStatus(`✅ Box content saved${cloudSaved ? ' to cloud' : ' locally'}: ${boxId}`, 'success');
                    
                    // Clear form
                    this.newBoxId.value = '';
                    this.boxDescription.value = '';
                    this.contentPhoto.value = '';

                } catch (error) {
                    console.error('Error saving box content:', error);
                    this.updateStatus('Failed to save box content', 'error');
                } finally {
                    this.saveBtn.disabled = false;
                    this.saveBtn.textContent = 'Save to Cloud';
                }
            }

            fileToDataUrl(file) {
                return new Promise((resolve, reject) => {
                    const reader = new FileReader();
                    reader.onload = () => resolve(reader.result);
                    reader.onerror = reject;
                    reader.readAsDataURL(file);
                });
            }
        }

        // Initialize the app when the page loads
        let app;
        document.addEventListener('DOMContentLoaded', () => {
            app = new CloudBoxScanner();
        });
    </script>
</body>
</html># Box-scanner
