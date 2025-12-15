> ⚠️ **Important Notice**
>
> This library is specifically adapted for using **iPerf with MCU-based Wi-Fi chipsets** (e.g. devices controlled via AT commands or running in constrained embedded environments).
>
> For **desktop or mobile platforms** (macOS / iOS / iPadOS) with full networking capabilities, it is **strongly recommended** to use the original `iperf-swift` implementation instead.
>
> The original `iperf-swift` source code can be found here:  
> https://github.com/igorskh/iperf-swift
>
> This repository focuses on modifications and adaptations required for **MCU-driven Wi-Fi workflows**, such as limited socket control, custom data paths, or non-standard network stacks.

# Swift wrapper for iPerf

An easy to use Swift wrapper for iPerf.

## Usage

An application using this package: [iPerf SwiftUI](https://github.com/igorskh/iperf-swiftui)

Package implements iPerf server and client.

Usage example:
```swift
class IperfRunnerController: ObservableObject, Identifiable {
    private var iperfRunner: IperfRunner?
    
    @Published var isDeleted = false
    @Published var runnerState: IperfRunnerState = .ready
    @Published var debugDescription: String = ""
    @Published var displayError: Bool = false
    @Published var results = [IperfIntervalResult]() {
        didSet {
            objectWillChange.send()
        }
    }
    
    func onResultReceived(result: IperfIntervalResult) {
        if result.streams.count > 0 {
            results.append(result)
        }
    }
    
    func onErrorReceived(error: IperfError) {
        DispatchQueue.main.async {
            self.displayError = error != .IENONE
            self.debugDescription = error.debugDescription
        }
    }
    
    func onNewState(state: IperfRunnerState) {
        if state != .unknown && state != runnerState {
            DispatchQueue.main.async {
                self.runnerState = state
            }
        }
    }
    
    func start() {
        self.formInput = formInput
        
        results = []
        debugDescription = ""
        
        iperfRunner = IperfRunner(with: IperfConfiguration())
        iperfRunner!.start(
            onResultReceived,
            onErrorReceived,
            onNewState
        )
    }
    
    func stop() {
        iperfRunner!.stop()
    }
}

```

## Not implemented

The code which requires OpenSSL library is currently commented. 
