import React, { useRef } from 'react';
import { StyleSheet, View } from 'react-native';
import { WebView } from 'react-native-webview';
import { StatusBar } from 'expo-status-bar';
import 'expo-dev-client';

const demoContent = `<!doctype html>
<html>
  <head>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/xterm/css/xterm.css" />
  <script src="https://cdn.jsdelivr.net/npm/xterm/lib/xterm.js"></script>
  </head>
  <body>
    <div id="terminal"></div>
    <script>
      var term = new Terminal({
        disableStdin: false
      });
      term.open(document.getElementById('terminal'));
      term.write('Hello from \x1B[1;3;31mxterm.js\x1B[0m $ ');

      var currentInput = '';

      // Function to post messages consistently
      function postMessage(type, data) {
        window.ReactNativeWebView.postMessage(JSON.stringify({ type, ...data }));
      }

      // Event listeners
      term.onData(data => {
        currentInput += data;
        postMessage('DATA', { data, currentInput });

        if (data === '\\r') {
          currentInput = '';
          postMessage('ENTERKEY', { currentInput });
        } else if (data === '\\u007f') { // Handle backspace
          currentInput = currentInput.slice(0, -1);
        }
      });

      term.onKey(e => {
        postMessage('KEY', { key: e.key, domEvent: e.domEvent });
      });

      term.onLineFeed(() => {
        postMessage('LINEFEED');
      });

      term.onScroll(position => {
        postMessage('SCROLL', { position });
      });

      term.onResize(size => {
        postMessage('RESIZE', { cols: size.cols, rows: size.rows });
      });

      term.onSelectionChange(() => {
        const selection = term.getSelection();
        postMessage('SELECTION_CHANGE', { selection });
      });

      term.onTitleChange(title => {
        postMessage('TITLE_CHANGE', { title });
      });

      term.onBell(() => {
        postMessage('BELL');
      });

      // Adding an input event listener to handle alphabet keys
      document.addEventListener('input', (event) => {
        const input = event.data || event.target.value;
        currentInput += input;
        postMessage('DATA', { data: input, currentInput });
      });

    </script>
  </body>
</html>`;

export default function App() {
  const webViewRef = useRef(null);

  return (
    <View style={styles.container}>
      <StatusBar style="auto" />
      <WebView
        ref={webViewRef}
        originWhitelist={['*']}
        source={{ html: demoContent }}
        style={styles.webview}
        javaScriptEnabled={true}
        domStorageEnabled={true}
        onMessage={(event) => {
          const message = JSON.parse(event.nativeEvent.data);
          switch (message.type) {
            case 'ENTERKEY':
              console.log('Enter key pressed with input: ', message.currentInput);
              break;
            case 'DATA':
              console.log('Current input: ', message.currentInput);
              break;
            case 'KEY':
              console.log('Key event: ', message.key, message.domEvent);
              break;
            case 'LINEFEED':
              console.log('Line feed occurred');
              break;
            case 'SCROLL':
              console.log('Scroll position: ', message.position);
              break;
            case 'RESIZE':
              console.log('Terminal resized to: ', message.cols, 'cols,', message.rows, 'rows');
              break;
            case 'SELECTION_CHANGE':
              console.log('Selection changed: ', message.selection);
              break;
            case 'TITLE_CHANGE':
              console.log('Title changed: ', message.title);
              break;
            case 'BELL':
              console.log('Bell event occurred');
              break;
            default:
              console.log('Unknown event: ', message);
          }
        }}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
  },
  webview: {
    flex: 1,
  },
});
