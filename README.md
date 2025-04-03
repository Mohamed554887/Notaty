# Notaty
import 'package:flutter/material.dart'; import 'package:record/record.dart'; import 'package:path_provider/path_provider.dart'; import 'dart:io'; import 'dart:async';

void main() { runApp(NotesApp()); }

class NotesApp extends StatelessWidget { @override Widget build(BuildContext context) { return MaterialApp( debugShowCheckedModeBanner: false, home: NotesPage(), ); } }

class NotesPage extends StatefulWidget { @override _NotesPageState createState() => _NotesPageState(); }

class _NotesPageState extends State<NotesPage> { final List<Map<String, dynamic>> notes = []; final TextEditingController _textController = TextEditingController(); final Record _audioRecorder = Record(); String? _recordingPath;

Future<void> startRecording() async { if (await audioRecorder.hasPermission()) { final dir = await getApplicationDocumentsDirectory(); final path = "${dir.path}/note${DateTime.now().millisecondsSinceEpoch}.m4a"; await _audioRecorder.start(path: path); setState(() { _recordingPath = path; }); } }

Future<void> stopRecording() async { final path = await _audioRecorder.stop(); if (path != null) { setState(() { notes.add({"type": "audio", "content": path}); _recordingPath = null; }); } }

void addTextNote() { if (_textController.text.isNotEmpty) { setState(() { notes.add({"type": "text", "content": _textController.text}); _textController.clear(); }); } }

void playAudio(String path) { // يمكن استخدام مكتبة مثل just_audio لتشغيل الملف الصوتي }

@override Widget build(BuildContext context) { return Scaffold( appBar: AppBar(title: Text("الملاحظات")), body: Column( children: [ Expanded( child: ListView.builder( itemCount: notes.length, itemBuilder: (context, index) { final note = notes[index]; if (note["type"] == "text") { return ListTile( title: Text(note["content"]), ); } else { return ListTile( title: Text("ملاحظة صوتية"), trailing: IconButton( icon: Icon(Icons.play_arrow), onPressed: () => playAudio(note["content"]), ), ); } }, ), ), Padding( padding: const EdgeInsets.all(8.0), child: Row( children: [ Expanded( child: TextField( controller: _textController, decoration: InputDecoration(hintText: "اكتب ملاحظة..."), ), ), IconButton( icon: Icon(Icons.send), onPressed: addTextNote, ), IconButton( icon: Icon(_recordingPath == null ? Icons.mic : Icons.stop), onPressed: _recordingPath == null ? startRecording : stopRecording, ), ], ), ), ], ), ); } }

