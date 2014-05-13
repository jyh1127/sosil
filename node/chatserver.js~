var mongo  = require('mongodb').MongoClient,
	client = require('socket.io').listen(8080).sockets,
	date   = require('date-utils');

mongo.connect('mongodb://127.0.0.1/chat', function(err, db) {
	if(err) throw err;

	client.on('connection', function(socket) {

		var col = db.collection('messages'),
			usr = db.collection('users'),
			sendStatus = function(s) {
				socket.emit('status', s);
			};

		// Emit all messages
		col.find().limit(100).sort({_id: 1}).toArray(function(err, res) {
			if(err) throw err;
			socket.emit('output', res);
		});

		// Wait for input
		socket.on('input', function(data) {
			var name = data.name,
				pass = data.pass,
				message = data.message,
				whitespacePattern = /^\s*$/,
				insertChat = function(name, message) {
					col.insert({
						name   : name,
						message: message,
						time   : new Date().toFormat('YYYY-MM-DD HH24:MI:SS')
					}, function() {

						// Emit latest message to ALL clients
						client.emit('output', [data]);

						sendStatus({
							message: "Message sent",
							clear: true
						});
					});
				};

			if(whitespacePattern.test(name)) {
				sendStatus('Name is required.');
			} else if(whitespacePattern.test(message)) {
				sendStatus('Message is required.');
			}else if(whitespacePattern.test(pass)) {
				sendStatus('Password is required.');
			} else {

				usr.find({name: name}).toArray(function(err, res) {
					if(err) throw err;
					
					if(res.length > 0) {
						if(res[0].pass == pass) {
							insertChat(name, message);
						} else {
							sendStatus('Password incorrect');
						}
					} else {
						usr.insert({
							name: name,
							pass: pass
						}, function() {
							insertChat(name, message)
						});
					}
				});

			}

		});

		socket.on('make-room', function(data) {

			var room = db.collection('rooms'),
				roomName = data.name;
		});

		socket.on('add-friend', function(data) {

			var user = db.collection('users'),
				frnd = db.collection('friends'),
				mid = data.userId,
				fid = data.friendId;

				user.find({_id:mid}).toArray() {

				}
		});

	});
});
