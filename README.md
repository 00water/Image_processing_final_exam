디지털 영상처리 기말고사
==================
1번 문제
---
도형을 인식하고, 외곽에 테두리 선으로 표시하고, 인식결과를 도형 속에 글씨로 나타내시오.   
![test](https://user-images.githubusercontent.com/105781767/206727809-4a5f74fb-60c1-4cd4-8f37-de88d9da7625.png)   

설명
---
구현에 앞서 교수님께서 디지털 영상처리 카페에 올려주신 9강의 유튜브 영상을 참조하여 Pycharm 환경에서 수행했습니다.   
https://www.youtube.com/watch?v=14z9mKlt4-o&t=55s   

기존의 검은색 바탕의 그림은 인식 단계에서 인식을 잘 하지 못하여, 반전 시킨 이미지를 넣어 실행하였습니다.   
![image](https://user-images.githubusercontent.com/105781767/206728335-9c8d575f-1315-41f2-a118-683508413ee5.png)

소스코드
---
~~~py
import cv2 as cv

def setLabel(image, str, contour):
    (text_width, text_height), baseline = cv.getTextSize(str, cv.FONT_HERSHEY_SIMPLEX, 0.7, 1)
    x,y,width,height = cv.boundingRect(contour)
    pt_x = x+int((width-text_width)/2)
    pt_y = y+int((height + text_height)/2)
    cv.rectangle(image, (pt_x, pt_y+baseline), (pt_x+text_width, pt_y-text_height), (200,200,200), cv.FILLED)
    cv.putText(image, str, (pt_x, pt_y), cv.FONT_HERSHEY_SIMPLEX, 0.7, (0,0,0), 1, 8)


img_color = cv.imread('test3.png', cv.IMREAD_COLOR)
cv.imshow('result', img_color)
cv.waitKey(0)

img_gray = cv.cvtColor(img_color, cv.COLOR_BGR2GRAY)
cv.imshow('result', img_gray)
cv.waitKey(0)

ret,img_binary = cv.threshold(img_gray, 127, 255, cv.THRESH_BINARY_INV|cv.THRESH_OTSU)
cv.imshow('result', img_binary)
cv.waitKey(0)

contours, hierarchy = cv.findContours(img_binary, cv.RETR_EXTERNAL, cv.CHAIN_APPROX_SIMPLE)

for cnt in contours:
    size = len(cnt)
    print(size)

    epsilon = 0.005 * cv.arcLength(cnt, True)
    approx = cv.approxPolyDP(cnt, epsilon, True)

    size = len(approx)
    print(size)

    cv.line(img_color, tuple(approx[0][0]), tuple(approx[size-1][0]), (0, 255, 0), 3)
    for k in range(size-1):
        cv.line(img_color, tuple(approx[k][0]), tuple(approx[k+1][0]), (0, 255, 0), 3)

    if cv.isContourConvex(approx):
        if size == 3:
            setLabel(img_color, "Triangle", cnt)
        elif size == 4:
            setLabel(img_color, "Rectangle", cnt)
        elif size == 5:
            setLabel(img_color, "Pentagon", cnt)
        elif size == 6:
            setLabel(img_color, "Hexagon", cnt)
        elif size == 16:
            setLabel(img_color, "Circle",cnt)
        else:
            setLabel(img_color, str(size), cnt)
    else:
        setLabel(img_color, str(size), cnt)

cv.imshow('result', img_color)
cv.waitKey(0)
~~~
실행화면
---
![스크린샷 2022-12-09 오후 11 39 19](https://user-images.githubusercontent.com/105781767/206729380-8d9b44ac-2a8e-466e-86db-0a8f09c9752f.png)   

--------------------------------------------------------------------------------------------
      
2번 문제
---
다른 사람 3명의 얼굴과 내 얼굴을 각각 3장식 모으고 각 사람의 2장은 학습에 사용하고, 나머지 1장으로 얼굴을 인식하는 프로그램을 구현하고 결과 사진과 코드를 GitHub에 올리고 링크를 제출하시오.

설명
---
유튜브 https://youtu.be/S21jhMfjBdc 영상을 참조하였습니다.

소스코드
---
- index.html
~~~html
>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="description" content="">
  <meta name="author" content="Mark Otto, Jacob Thornton, and Bootstrap contributors">
  <meta name="generator" content="Hugo 0.80.0">
  <title>Opencv.js 안면 인식</title>

  <link rel="canonical" href="https://getbootstrap.com/docs/5.0/examples/album/">



  <!-- Bootstrap core CSS -->
  <link href="assets/dist/css/bootstrap.min.css" rel="stylesheet">

  <style>
    .bd-placeholder-img {
      font-size: 1.125rem;
      text-anchor: middle;
      -webkit-user-select: none;
      -moz-user-select: none;
      user-select: none;
    }

    @media (min-width: 768px) {
      .bd-placeholder-img-lg {
        font-size: 3.5rem;
      }
    }
  </style>


</head>

<body>
  <header>
    <div class="collapse bg-dark" id="navbarHeader">
      <div class="container">
        <div class="row">
          <div class="col-sm-8 col-md-7 py-4">
            <h4 class="text-white">About</h4>
            <p class="text-muted">Opencv.js 실시간 AI 안면 인식</p>
          </div>
        </div>
      </div>
    </div>
    <div class="navbar navbar-dark bg-dark shadow-sm">
      <div class="container">
        <a href="#" class="navbar-brand d-flex align-items-center">
          <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="none" stroke="currentColor"
            stroke-linecap="round" stroke-linejoin="round" stroke-width="2" aria-hidden="true" class="me-2"
            viewBox="0 0 24 24">
            <path d="M23 19a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h4l2-3h6l2 3h4a2 2 0 0 1 2 2z" />
            <circle cx="12" cy="13" r="4" />
          </svg>
          <strong>Opencv.js</strong>
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarHeader"
          aria-controls="navbarHeader" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
      </div>
    </div>
  </header>

  <main>

    <section class="py-5 text-center container">
      <div class="row py-lg-5">
        <div class="col-lg-6 col-md-8 mx-auto">
          <h1 class="fw-light">실시간 AI 안면 인식</h1>
          <p class="lead text-muted" id="status">OpenCV.js is loading...</p>
          <div class="spinner-border text-primary" role="status" id="loader">
            <span class="sr-only"></span>
          </div>
        </div>
      </div>
    </section>

    <div class="album py-5 bg-light">
      <div class="container">
        <div class="row row-cols-1 g-3 text-center">
          <div class="col">
            <div class="card shadow-sm">
              <canvas id="output" width=640 height=480 style="max-width: 100%"></canvas>
              <div class="card-body">
                <div class='py-2'>
                  <table>
                    <tr id="targetImgs"></tr>
                    <tr id="targetNames"></tr>
                  </table>
                </div>
                <div class='py-2'>
                  <button id="addPersonButton" type="button" disabled="true" class="btn btn-sm btn-outline-secondary">사람
                    추가</button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

  </main>



  <script src="assets/dist/js/bootstrap.bundle.min.js"></script>

  <script type='text/javascript'>

    var netDet = undefined, netRecogn = undefined;
    var persons = {};

    //! [Run face detection model]
    function detectFaces(img) {
      var blob = cv.blobFromImage(img, 1, { width: 192, height: 144 }, [104, 117, 123, 0], false, false);
      netDet.setInput(blob);
      var out = netDet.forward();

      var faces = [];
      for (var i = 0, n = out.data32F.length; i < n; i += 7) {
        var confidence = out.data32F[i + 2];
        var left = out.data32F[i + 3] * img.cols;
        var top = out.data32F[i + 4] * img.rows;
        var right = out.data32F[i + 5] * img.cols;
        var bottom = out.data32F[i + 6] * img.rows;
        left = Math.min(Math.max(0, left), img.cols - 1);
        right = Math.min(Math.max(0, right), img.cols - 1);
        bottom = Math.min(Math.max(0, bottom), img.rows - 1);
        top = Math.min(Math.max(0, top), img.rows - 1);

        if (confidence > 0.5 && left < right && top < bottom) {
          faces.push({ x: left, y: top, width: right - left, height: bottom - top })
        }
      }
      blob.delete();
      out.delete();
      return faces;
    };
    //! [Run face detection model]

    //! [Get 128 floating points feature vector]
    function face2vec(face) {
      var blob = cv.blobFromImage(face, 1.0 / 255, { width: 96, height: 96 }, [0, 0, 0, 0], true, false)
      netRecogn.setInput(blob);
      var vec = netRecogn.forward();
      blob.delete();
      return vec;
    };
    //! [Get 128 floating points feature vector]

    //! [Recognize]
    function recognize(face) {
      var vec = face2vec(face);
      var bestMatchName = 'unknown';
      var bestMatchScore = 0.5;  // Actually, the minimum is -1 but we use it as a threshold.
      for (name in persons) {
        var personVec = persons[name];
        var score = vec.dot(personVec);
        if (score > bestMatchScore) {
          bestMatchScore = score;
          bestMatchName = name;
        }
      }
      vec.delete();
      return bestMatchName;
    };
    //! [Recognize]

    function loadModels(callback) {
      var utils = new Utils('');
      var proto = 'https://raw.githubusercontent.com/opencv/opencv/master/samples/dnn/face_detector/deploy_lowres.prototxt';
      var weights = 'https://raw.githubusercontent.com/opencv/opencv_3rdparty/dnn_samples_face_detector_20180205_fp16/res10_300x300_ssd_iter_140000_fp16.caffemodel';
      var recognModel = 'https://raw.githubusercontent.com/pyannote/pyannote-data/master/openface.nn4.small2.v1.t7';
      utils.createFileFromUrl('face_detector.prototxt', proto, () => {
        document.getElementById('status').innerHTML = 'Downloading face_detector.caffemodel';
        utils.createFileFromUrl('face_detector.caffemodel', weights, () => {
          document.getElementById('status').innerHTML = 'Downloading OpenFace model';
          utils.createFileFromUrl('face_recognition.t7', recognModel, () => {
            document.getElementById('status').innerHTML = '';
            netDet = cv.readNetFromCaffe('face_detector.prototxt', 'face_detector.caffemodel');
            netRecogn = cv.readNetFromTorch('face_recognition.t7');
            callback();
          });
        });
      });
    };



    function main() {
      // Create a camera object.
      var output = document.getElementById('output');
      var camera = document.createElement("video");
      camera.setAttribute("width", output.width);
      camera.setAttribute("height", output.height);

      // Get a permission from user to use a camera.
      navigator.mediaDevices.getUserMedia({ video: true, audio: false })
        .then(function (stream) {
          camera.srcObject = stream;
          camera.onloadedmetadata = function (e) {
            camera.play();
          };
        });

      //! [Open a camera stream]
      var cap = new cv.VideoCapture(camera);
      var frame = new cv.Mat(camera.height, camera.width, cv.CV_8UC4);
      var frameBGR = new cv.Mat(camera.height, camera.width, cv.CV_8UC3);
      //! [Open a camera stream]

      //! [Add a person]
      document.getElementById('addPersonButton').onclick = function () {
        var rects = detectFaces(frameBGR);
        if (rects.length > 0) {
          var face = frameBGR.roi(rects[0]);

          var name = prompt('이름을 알려주세요:');
          var cell = document.getElementById("targetNames").insertCell(0);
          cell.innerHTML = name;

          persons[name] = face2vec(face).clone();

          var canvas = document.createElement("canvas");
          canvas.setAttribute("width", 96);
          canvas.setAttribute("height", 96);
          var cell = document.getElementById("targetImgs").insertCell(0);
          cell.appendChild(canvas);

          var faceResized = new cv.Mat(canvas.height, canvas.width, cv.CV_8UC3);
          cv.resize(face, faceResized, { width: canvas.width, height: canvas.height });
          cv.cvtColor(faceResized, faceResized, cv.COLOR_BGR2RGB);
          cv.imshow(canvas, faceResized);
          faceResized.delete();
        }
      };
      //! [Add a person]

      const FPS = 30;  // Target number of frames processed per second.
      function captureFrame() {
        var begin = Date.now();
        cap.read(frame);  // Read a frame from camera
        cv.cvtColor(frame, frameBGR, cv.COLOR_RGBA2BGR);

        var faces = detectFaces(frameBGR);
        faces.forEach(function (rect) {
          cv.rectangle(frame, { x: rect.x, y: rect.y }, { x: rect.x + rect.width, y: rect.y + rect.height }, [0, 255, 0, 255]);

          var face = frameBGR.roi(rect);
          var name = recognize(face);
          cv.putText(frame, name, { x: rect.x, y: rect.y }, cv.FONT_HERSHEY_SIMPLEX, 1.0, [0, 255, 0, 255]);
        });

        cv.imshow(output, frame);

        // Loop this function.
        var delay = 1000 / FPS - (Date.now() - begin);
        setTimeout(captureFrame, delay);

      };

      loadModels(function () {
        captureFrame();
        document.getElementById('addPersonButton').disabled = false;
        console.log('captureFrame')
      });

    };

    function onOpenCvReady() {
      cv['onRuntimeInitialized'] = () => {
        main();

        document.getElementById('loader').style.display = "none";
      }
    }
  </script>

  <script src="js/utils.js" type="text/javascript"></script>
  <script async src="js/opencv.js" onload="onOpenCvReady();" type="text/javascript"></script>

</body>

</html>
~~~

실행화면
---
- 3명 단체사진 인식
![스크린샷 2022-11-27 오후 6 08 23](https://user-images.githubusercontent.com/105781767/206784433-a742228c-85e2-4b8c-91fe-b6360d7bdad2.png)
   
- 내 사진 인식
![스크린샷 2022-11-27 오후 5 47 45](https://user-images.githubusercontent.com/105781767/206784555-f23c0dc6-7efb-40ab-b89d-97903970eb66.png)













