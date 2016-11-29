# Dark Launch on iOS

Dark Launch is a powerful tool that all developers should become familiar with. Also known as feature flags, it allows for an app's behaviour to be configured remotely, without having to update the app on the device.

[GroundControl](https://github.com/mattt/GroundControl) makes implementing Dark Launch on iOS very simple. To set it up, all you need are a few lines of Swift, and a simple server. I'll show you how with Python Flask.


Swift code (can be placed in AppDelegate.swift):

    import GroundControl
    // ...
    var url = URL(string: "http://localhost:5000/features.plist")!
    UserDefaults.standard.register(
        with: url,
        success: { dict in
            print("key1:")
            print(UserDefaults.standard.object(forKey: "key1"))
        },
        failure: { err in
            print("GroundControl failure: \(err)")
        }
    )

`server.py` code:

    from plistlib import writePlistToString
    from flask import Flask, make_response
    app = Flask(__name__)


    @app.route('/features.plist')
    def features():
        plist = {
             'key1': 'value1',
             'key2': 3.14,
             'key3': True,
        }
        return make_response(
            writePlistToString(plist),
            200,
            {'Content-Type': 'application/x-plist'}  # GroundControl requires this to be set
        )


    if __name__ == "__main__":
        app.run()

To run the server, run this in a terminal:

    pip install flask
    export FLASK_APP=server.py
    flask run --host=0.0.0.0

Note: You'll want to use `0.0.0.0` so the server is publicly available. This is necessary if testing on a physical iOS device.

And that's it! Now when the server response is modified, the app will behave accordingly!
