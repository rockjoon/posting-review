## 1. 오픈소스 소프트웨어(Open Source Software, OSS)
 - 공개적으로 액세스할 수 있게 설계되어 누구나 자유롭게 확인, 수정, 배포할 수 있는 코드입니다.

## 2. 오픈소스 장점
1. 비용 절감
2. 빠르고 유연한 개발 
3. 신뢰성(검증된 기관에서 발행 시 !!)
4. 변경의 자유로움(오픈소스가 유료화 되어도 자체적으로 변경하여 자산화 시 오픈소스 계속 이용 가능)

## 3. Cropper.js 오픈소스 소개
- 공식 홈페이지  - [Cropper.js](https://fengyuanchen.github.io/cropperjs/)
- GitHub 주소 - [GitHub - fengyuanchen/cropperjs: JavaScript image cropper.](https://github.com/fengyuanchen/cropperjs)

- 	JavaScript의 Container와 Canvas 기능을 활용한 오픈소스로 Canvas 안 이미지를 자유롭게 편집하고  Canvas를 이미지 파일로 쉽게 저장할 수 있도록 도와준다.
- 라이선스 : MIT

## 4. 사용 방법
```
<!-- Wrap the image or canvas element with a block element (container) -->
<div>
  <img id="image" src="picture.jpg">
</div>

```
```
/* Ensure the size of the image fit the container perfectly */
img {
  display: block;

  /* This rule is very important, please don't ignore this */
  max-width: 100%;
}

```
```
// import 'cropperjs/dist/cropper.css';
import Cropper from 'cropperjs';

const image = document.getElementById('image');
const cropper = new Cropper(image, {
  aspectRatio: 16 / 9,
  crop(event) {
    console.log(event.detail.x);
    console.log(event.detail.y);
    console.log(event.detail.width);
    console.log(event.detail.height);
    console.log(event.detail.rotate);
    console.log(event.detail.scaleX);
    console.log(event.detail.scaleY);
  },
});

```


![20201126-image1](Image/20201126-image1.jpg)

> https://github.com/fengyuanchen/cropperjs/raw/master/docs/images/layers.jpg  

![20201126-image2](Image/20201126-image2.jpg)
		
> https://github.com/fengyuanchen/cropperjs/raw/master/docs/images/data.jpg  


#### Options(Object)
`new Cropper(image, options)`
`Cropper.setDefualts(options)`

- viewMode 
	- 이미지의 크기를 벗어날것인가 이미지 크기 안에서 crop을 지정할 것인가를 정한다.
- dragMode
	- 마우스 포인터의 역할(move, crop, none)
- preview
	- Crop 되는 이미지를 실시간으로 보여주는 div를 설정
- restore
	- cropper의 영역을 재 시작한다.
- checkCrossOrigin
- autoCropArea
	- cropper 시작시 CopperBox의 크기 지정(0.1~1.0)
- zoomOnWheel
	- 마우스 휠을 이용한 zoom 여부
- cropBoxResizeable
	- CropperBox의 사이즈 고정
- toggleDragModeOnDbclick
	- Container안에서 마우스 더블 클릭 시 dragMode 변환
#### Methods
- getData()
	- 최종적으로 잘라낸 Cropper 위치와 크기데이터를 원본 이미지에 기반해 출력
- getContainerData()
	- Container의 크기를 반환한다.
- getImageData()
	- Image의 크기를 반환한다.
- getCanvasData()
	- Canvas의 크기를 반환한다.
- getCropBoxData()
	- CropBox의 크기를 가져온다
- getCroppedCanvas([options])
	- Cropper된 이미지를 가져온다.
	- 최종적으로 많은 옵션을 지정해서 Image를 얻을 수 있는 메소드
	- options
		- fillColor
			- 투명도 사용시 무조건 이미지 png로 저장
			(jpeg, jpg는 투명도를 검정색으로 나타낸다….   ~~삽질,,,,,,의 ,,,, 결,,,고,,,ㅏ~~ )
		- imageSmoothingEnalbed 
			- 이미지 매끄럽게 처리되는 지 여부
		- imageSmoothingQulity
			- 이미지 품질 낮음, 중간, 높음 설정
	-**return type : HTMLCanvasElement** 

#### Event
- ready
- cropstart
- cropmove
- cropend 
*거의 log용으로 많이 활용하는거 같다,,*

###  Container에서 이미지파일로  저장 방법

1. Canvas -> blob
2. Blob -> FormData로 넘기기
3. FormData에서 서버로 넘기기
**더 나은 이미지 품질을 위 원본, 잘린 이미지 서버에 업로드 후 서버에서 자르기 실행(손실압축)**

```
//1. 기본 형식
cropper.getCroppedCanvas();

//2. Options사용 방식
cropper.getCroppedCanvas({
  width: 160,
  height: 90,
  minWidth: 256,
  minHeight: 256,
  maxWidth: 4096,
  maxHeight: 4096,
  fillColor: '#fff',//투명도 설정 가능
  imageSmoothingEnabled: false,
  imageSmoothingQuality: 'high',
});

//3. 서버에 올릴 수 있도록 파일로 변환하는 방법
cropper.getCroppedCanvas().toBlob((blob) => {
//HTMLCanvasElement를 return 받아서 blob파일로 변환해준다
  const formData = new FormData();

	formData.append('croppedImage', blob/*, 'example.png' , 0.7*/);
	//새로운 formData를 생성해서 앞에서 변경해준 blob파일을 삽입한다.(이름 지정 가능, 맨뒤 매개변수는 화질 설정)
  // jQuery.ajax이용해서 서버에 업로드
  $.ajax('/path/to/upload', {
    method: 'POST',
    data: formData,//앞에서 생성한 formData
  	  processData : false,	// data 파라미터 강제 string 변환 방지
    contentType : false,	// application/x-www-form-urlencoded; 방지
    success() {
      console.log('Upload success');
    },
    error() {
      console.log('Upload error');
    },
  });
}/*, 'image/png' */);//서버에 저장 형식 사용 가능

```
- - - -

**거의 무조건 png 저장을 추천드립니다…**


## 5. 적용방법
 **사용자 요구에 맞게 오픈소스 변경하기 위해 CDN 방식을 미 사용**
- 처음 Cropper시 이미지 안에서만 생성 될 수 있도록 되어있던 코드 변경하여 원하는 CropBox를 생성하고 그 크기에 이미지를 맞추는 방법으로 변경
![20201126-image3](Image/20201126-image3)

- CropBox 사용자 크기 지정 가능케 변경
![20201126-image4](Image/20201126-image3.png)
Ss







