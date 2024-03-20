import SwiftUI

import AVFoundation

import Combine

class AudioManager: NSObject, AVAudioPlayerDelegate {
    static let shared = AudioManager()

    private var audioPlayer: AVAudioPlayer?

    private override init() {
        super.init()
    }

    func playPopSound() {
        guard let url = Bundle.main.url(forResource: "popSound", withExtension: "aiff") else {
            print("Error: Audio file not found")
            return
        }

        DispatchQueue.global().async {
            do {
                self.audioPlayer = try AVAudioPlayer(contentsOf: url)
                self.audioPlayer?.delegate = self
                self.audioPlayer?.prepareToPlay()

                DispatchQueue.main.async {
                    self.audioPlayer?.play()
                }

            } catch let error as NSError {
                print("Error playing sound: \(error.localizedDescription)")
            }
        }
    }


    func audioPlayerDidFinishPlaying(_ player: AVAudioPlayer, successfully flag: Bool) {
        if flag {
            // Audio finished playing
            audioPlayer = nil
        }
    }
}



struct ContentView: View {

    var body: some View {

        IntroView()

    }

}



struct VibrantBackground: View {
    @Binding var gradientOffset: CGFloat

    var body: some View {
        LinearGradient(
            gradient: Gradient(colors: [
                Color(hue: 0.5, saturation: 0.7, brightness: 0.8),
                Color(hue: 0.6, saturation: 0.7, brightness: 0.8),
                Color(hue: 0.7, saturation: 0.7, brightness: 0.8),
                Color(hue: 0.8, saturation: 0.7, brightness: 0.8)
            ]),
            startPoint: .top,
            endPoint: .bottom
        )
        .overlay(
            RoundedRectangle(cornerRadius: 10)
                .stroke(Color.white, lineWidth: 5)
                .blur(radius: 10)
                .offset(x: -5, y: -5)
                .mask(
                    RoundedRectangle(cornerRadius: 10)
                        .fill(LinearGradient(gradient: Gradient(colors: [.black, .clear]), startPoint: .topLeading, endPoint: .bottomTrailing))
                )
                .offset(x: gradientOffset, y: -gradientOffset)
                .onAppear {
                    withAnimation(Animation.easeInOut(duration: 2).repeatForever(autoreverses: true)) {
                        gradientOffset = 100
                    }
                }
        )
    }
}




struct CircleView: View {

    let isPopping: Bool

    let onTap: () -> Void



    var body: some View {

        SphereView(isPopping: isPopping, onTap: onTap)

            .overlay(

                Image(systemName: isPopping ? "heart.fill" : "")

                    .font(.system(size: 30))

                    .foregroundColor(isPopping ? Color.black : Color.white)

            )

    }

}



struct SphereView: View {
    let isPopping: Bool
    let onTap: () -> Void

    @State private var color: Color = Color.blue
    @State private var showHeart: Bool = false

    var body: some View {
        ZStack {
            Circle()
                .frame(width: 70, height: 70)
                .foregroundColor(color)
                .overlay(
                    Circle()
                        .stroke(isPopping ? Color.red : Color.clear, lineWidth: isPopping ? 4 : 0)
                        .blur(radius: isPopping ? 4 : 0)
                        .offset(x: isPopping ? 2 : 0, y: isPopping ? 2 : 0)
                )
                .onTapGesture {
                    debounceTap()
                }

            if showHeart {
                Image(systemName: "heart.fill")
                    .font(.system(size: 20))
                    .foregroundColor(Color.red)
                    .offset(y: -15)
                    .opacity(0.8)
            }
        }
        .onChange(of: isPopping) { newValue, _ in
            if !newValue {
                withAnimation(Animation.easeInOut(duration: 2).repeatForever()) {
                    color = Color(hue: Double.random(in: 0..<1), saturation: 0.8, brightness: 0.8)
                }
            } else {
                withAnimation(Animation.easeInOut(duration: 2).repeatForever()) {
                    color = Color(hue: Double.random(in: 0..<1), saturation: 0.8, brightness: 0.8)
                }
            }
        }
    }

    private func debounceTap() {
        // Debounce taps to prevent rapid firing
        guard !showHeart else { return }
        onTap()
        withAnimation(Animation.easeInOut(duration: 0.1)) {
            if isPopping {
                AudioManager.shared.playPopSound()
                showHeart = true
                color = Color(red: 0.8, green: 0, blue: 0)
                DispatchQueue.main.asyncAfter(deadline: .now() + 0.5) {
                    withAnimation {
                        showHeart = false
                    }
                }
            }
        }
    }
}




struct GridStack<Content: View>: View {

    let rows: Int

    let columns: Int

    let content: (Int, Int) -> Content



    var body: some View {

        VStack(spacing: 0) {

            ForEach(0..<rows, id: \.self) { row in

                HStack(spacing: 0) {

                    ForEach(0..<columns, id: \.self) { col in

                        content(row, col)

                    }

                }

            }

        }

    }

}



struct ContentView_Previews: PreviewProvider {

    static var previews: some View {

        ContentView()

    }

}



struct IntroView: View {
    @State private var showGame = false
    @State private var infinitePlayPressed = false
    @State private var gradientOffset: CGFloat = 0 // Add this state
    @State private var showWarning = true // Add this state

    var body: some View {
        ZStack {
            VibrantBackground(gradientOffset: $gradientOffset) // Pass the state to VibrantBackground
                .edgesIgnoringSafeArea(.all)

            VStack {
                Spacer()

                if showWarning {
                    RoundedRectangle(cornerRadius: 10)
                        .fill(Color.white)
                        .frame(width: 300, height: 80)
                        .overlay(
                            Text("Warning: Bright Flashing Lights!")
                                .foregroundColor(.red)
                                .font(Font.custom("Avenir-Heavy", size: 16)) // Adjust the font size here
                                .padding()
                                .multilineTextAlignment(.center)
                        )
                        .padding()
                        .transition(.opacity) // Ensure that the view doesn't disappear
                }

                Text("ZTapz")
                    .font(Font.custom("Avenir-Heavy", size: 40))
                    .overlay(
                        LinearGradient(
                            gradient: Gradient(colors: [
                                Color(hue: 0.1, saturation: 0.9, brightness: 0.9),
                                Color(hue: 0.5, saturation: 0.9, brightness: 0.9),
                                Color(hue: 0.9, saturation: 0.9, brightness: 0.9),
                            ]),
                            startPoint: .topLeading,
                            endPoint: .bottomTrailing
                        )
                        .mask(Text("ZTapz").font(Font.custom("Avenir-Heavy", size: 40)))
                    )
                    .padding()

                Button("Quick Minute") {
                    showGame = true
                }
                .padding()
                .background(Color.green)
                .foregroundColor(.white)
                .cornerRadius(10)
                .fullScreenCover(isPresented: $showGame, content: {
                    GameView(isOneMinuteMode: true)
                })

                Text("Press Quick Minute to tap the moving heart as many times as you can in one minute.")
                    .foregroundColor(.white)
                    .font(.headline)
                    .multilineTextAlignment(.center)
                    .padding()
                    .fixedSize(horizontal: false, vertical: true)

                Spacer()
            }
        }
        .onAppear {
            withAnimation(Animation.easeInOut(duration: 2).repeatForever(autoreverses: true)) {
                gradientOffset = 100
            }
        }
    }
}













class GameViewModel: ObservableObject {

    @Published var timeCounter = 60

    // ... other properties and methods

}

struct GameView: View {
    @StateObject private var viewModel = GameViewModel()
    @State private var popping: [Bool]?
    @State private var score = 0
    @State private var targetIndex = 0
    @State private var isOneMinuteMode: Bool
    @State private var gameTimer: Timer?
    @State private var showBackButton = true
    @State private var isGameOver = false
    @State private var isTimerRunning = false
    @State private var animationSpeed: Double = 4
    @State private var gradientOffset: CGFloat = 0 // Add this state

    private var timerPublisher: Timer.TimerPublisher {
        Timer.publish(every: 1.0, on: .main, in: .common)
    }

    @Environment(\.presentationMode) var presentationMode

    init(isOneMinuteMode: Bool = false) {
        _isOneMinuteMode = State(initialValue: isOneMinuteMode)
        _animationSpeed = State(initialValue: 4)
    }

    var body: some View {
        ZStack {
            VibrantBackground(gradientOffset: $gradientOffset)
                .edgesIgnoringSafeArea(.all)

            VStack(alignment: .center) {
                Spacer()

                HStack {
                    Text("Time: \(viewModel.timeCounter)")
                        .foregroundColor(.white)
                        .padding(.leading, 20)

                    Spacer()

                    Text("Score: \(score)")
                        .font(.title2)
                        .foregroundColor(.white)
                        .padding(.trailing, 20)
                }
                .padding(.top, 10)

                if showBackButton {
                    HStack {
                        Spacer()
                        Button(action: {
                            resetGame()
                            presentationMode.wrappedValue.dismiss()
                            showBackButton = false
                        }) {
                            Text("Return to Menu")
                                .font(.headline)
                                .foregroundColor(.white)
                                .frame(width: 200, height: 50)
                                .background(Color.black)
                                .cornerRadius(10)
                                .padding(.top, 5)
                        }
                        Spacer()
                    }
                }

                HStack {
                    Text("Heart Speed")
                        .foregroundColor(.white)
                        .padding(.leading, 20)
                    Slider(value: $animationSpeed, in: 2...5, step: 0.1)

                        .padding()
                        .foregroundColor(.white)
                        .rotationEffect(.degrees(180))
                    Spacer()
                }

                if let poppingArray = popping {
                    GridStack(rows: 7, columns: 4) { row, col in
                        CircleView(
                            isPopping: poppingArray[row * 4 + col],
                            onTap: {
                                handleTap(tappedIndex: row * 4 + col)
                            }
                        )
                        .aspectRatio(1, contentMode: .fit)
                        .padding(5)
                        .animation(
                            Animation.easeInOut(duration: animationSpeed > 0 ? 4.0 / animationSpeed : .infinity)
                                .repeatForever(autoreverses: true),
                            value: poppingArray[row * 4 + col]
                        )
                    }
                    .padding([.leading, .trailing], 20)
                }

                HStack {
                    Spacer()
                    if !isTimerRunning {
                        Button(action: {
                            startOneMinuteGame()
                            startTimer()
                        }) {
                            Text("Start Timer")
                                .foregroundColor(.white)
                                .padding()
                                .frame(width: 200, height: 50)
                                .background(Color.blue)
                                .cornerRadius(10)
                                .padding(.top, 5)
                        }

                    }
                    Spacer()
                }

                Spacer()
            }
            .padding()
            .background(isGameOver ? Color.black : Color.clear)
            .alert(isPresented: $isGameOver) {
                Alert(
                    title: Text("GAME OVER!"),
                    message: Text(isOneMinuteMode ? "Your time is up!" : "Your score: \(score)"),
                    dismissButton: .default(Text("Play Again")) {
                        startOneMinuteGame()
                    }
                )
            }
        }
        .onAppear {
            popping = nil
        }
        .onDisappear {
            stopGame()
        }
        .onReceive(timerPublisher) { _ in
            if isTimerRunning && viewModel.timeCounter > 0 {
                viewModel.timeCounter -= 1
                startGame()
            } else {
                stopGame()
                isGameOver = true
                stopTimer()
            }
        }
    }

    private func resetGame() {
        viewModel.timeCounter = 60
        score = 0
        popping = nil
        showBackButton = true
        isGameOver = false
    }

    private func handleTap(tappedIndex: Int) {
        if tappedIndex == targetIndex {
            DispatchQueue.main.async {
                AudioManager.shared.playPopSound()
                score += 1
                targetIndex = Int.random(in: 0..<28)
                popping?[tappedIndex] = false
                popping?[targetIndex] = true
            }
        }
    }

    private func stopGame() {
        gameTimer?.invalidate()
        gameTimer = nil
        targetIndex = 0
        popping = nil
        isOneMinuteMode = false
        isTimerRunning = false
    }

    private func startGame() {
        targetIndex = Int.random(in: 0..<28)
        popping = Array(repeating: false, count: 28)
        popping?[targetIndex] = true
    }

    private func startOneMinuteGame() {
        gameTimer?.invalidate()
        gameTimer = nil
        score = 0
        viewModel.timeCounter = 60
        popping = Array(repeating: false, count: 28)
    }

    private func startTimer() {
        stopGame()
        resetGame()
        viewModel.timeCounter = 60

        gameTimer = Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { timer in
            guard viewModel.timeCounter > 0 else {
                stopGame()
                isGameOver = true
                stopTimer()
                return
            }

            DispatchQueue.main.async {
                viewModel.timeCounter -= 1
                startGame()
            }
        }

        isTimerRunning = true
    }

    private func stopTimer() {
        gameTimer?.invalidate()
        isTimerRunning = false
    }
}
