# 2024-NC2-A30-Video
![NC2 Slide 16_9 (1)](https://github.com/DeveloperAcademy-POSTECH/2024-NC2-A30-Video/assets/71865277/0787f073-6a79-4014-9f36-a838d4e0a444)


## 🎥 Youtube Link
(추후 만들어진 유튜브 링크 추가)

 <br/>

## 💡 About Video

- 영상을 다루는 Video에 어떤 기술들이 있는지 알게 되었다.
- AVKit을 활용하면 자막, 캡션, 속도 조절 등의 기능을 가진 Media Playback(영상 플레이어)과 PiP(Picture in Picture)를 구현할 수 있다는 것을 알게 되었다.
- AVKit의 AVPlayer와 AVPlayerViewController를 활용해서 플레이어를 구현할 수 있게 되었다.

 <br/>

## 🎯 What I focus on?
- ARKit을 활용하여 ARSession과 EyeTracking을 통합
- AVKit을 이용해 동영상 플레이어를 구현
- 사용자의 눈 움직임과 눈 깜박임을 감지하여 영상을 제어하는 기술을 중점적으로 다룸

<br/>

## 💼 Use Case
> 사용자가 오른쪽으로 눈을 움직이면 자동으로 다음 동영상이 재생되며, 눈 깜박임을 감지하여 일시정지 또는 재생을 제어할 수 있습니다.
- 요리 중이거나, 홈트를 하거나, 필기하면서 영상을 볼 때와 같은 상황에서 유용합니다.
- 손이 불편한 사람들이 핸즈프리 컨트롤을 쉽게 할 수 있도록 도울 수 있습니다.


 <br/>

## 🖼️ Prototype
![NC2 Mockups](https://github.com/DeveloperAcademy-POSTECH/2024-NC2-A30-Video/assets/71865277/a6c117e4-c9ed-4b9f-acf1-c8b20432f512)
- 메인 화면에서 간단한 온보딩 문구를 확인할 수 있습니다.
- **시작하기** 버튼을 눌러 영상 시청을 시작합니다.
- 영상의 시간이 다 되면 다음 영상으로 자동으로 넘어갑니다.
- 다만 중간에 다음 영상으로 이동하고 싶을 경우, 눈치를 보듯 👀 **오른쪽 끝으로 시선을 두었다 돌아오면** 다음으로 넘길 수 있습니다.

 <br/>

## 🛠️ About Code
이번 프로젝트에서는 AVKit과 ARKit을 활용하여 사용자의 눈 움직임과 깜박임으로 영상을 제어하는 기능을 구현했습니다. 아래는 주요 코드 부분입니다.

### AVKit을 활용한 동영상 플레이어 구현

**AVPlayer와 AVPlayerViewController 설정**
   ```swift
   var player: AVPlayer?
   var playerController: AVPlayerViewController?
   
   @objc func playVideo() {
       guard currentVideoIndex < videos.count else {
           currentVideoIndex = 0
           return
       }
       
       let videoName = videos[currentVideoIndex]
       guard let path = Bundle.main.path(forResource: videoName, ofType: "mp4") else {
           debugPrint("MP4 Not Found")
           return
       }
       
       let url = URL(fileURLWithPath: path)
       let playerItem = AVPlayerItem(url: url)
       
       NotificationCenter.default.addObserver(self, selector: #selector(videoDidEnd), name: .AVPlayerItemDidPlayToEndTime, object: playerItem)
       
       player = AVPlayer(playerItem: playerItem)
       playerController = AVPlayerViewController()
       playerController?.player = player
       
       present(playerController!, animated: true) {
           self.player?.play()
       }
       
       startSession()
   }
```

### ARKit과 EyeTracking을 통한 눈 움직임 감지

**세션 설정 및 시작**
```swift
func setupARSession() {
    session = ARSession()
    session.delegate = self
    
    let configuration = ARFaceTrackingConfiguration()
    configuration.isLightEstimationEnabled = true
    session.run(configuration, options: [])
}

@objc func startSession() {
    eyeTracking?.startSession()
    eyeTracking?.showPointer()
    
    let configuration = ARFaceTrackingConfiguration()
    configuration.isLightEstimationEnabled = true
    session.run(configuration, options: [])
}

@objc func endSession() {
    eyeTracking?.hidePointer()
    eyeTracking?.endSession()
    
    session.pause()
}
```

**눈 움직임을 통한 동영상 제어**
```swift
@objc func checkGazePosition() {
    guard let currentSession = eyeTracking?.currentSession, let lastGaze = currentSession.scanPath.last else { return }
    
    if lastGaze.x < 0 {
        print("Gaze exited to the right")
        endSession()
        playNextVideo()
    }
}
```
