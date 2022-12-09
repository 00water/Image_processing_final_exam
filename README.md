디지털 영상처리 기말고사
==================
1번 문제
---
도형을 인식하고, 외곽에 테두리 선으로 표시하고, 인식결과를 도형 속에 글씨로 나타내시오.   
![test](https://user-images.githubusercontent.com/105781767/206727809-4a5f74fb-60c1-4cd4-8f37-de88d9da7625.png)   

설명
---
구현에 앞서 교수님께서 디지털 영상처리 카페에 올려주신 9강의 유튜브 영상을 참조하였습니다.   
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

