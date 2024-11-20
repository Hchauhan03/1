<?php
include 'header.php';
include 'validation.php';
include 'leagueValidation.php';
require_once 'client_rmq_db.php';

// Fetch the leaderboard
$request = array();
$request['type'] = 'get_leaderboard';
$request['user_email'] = $leagueId;

$leaderboardResponse = createRabbitMQClientDatabase($request);
$leaderboard = $leaderboardResponse['leaderboard'];

// Fetch the messages for the league
$request = array();
$request['type'] = 'get_messages';
$request['league_id'] = $leagueId;

$messagesResponse = createRabbitMQClientDatabase($request);
$messages = $messagesResponse['messages'];

// Handle message posting
if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['message'])) {
    	$messageContent = trim($_POST['message']);

    	if (!empty($messageContent)) {
        	// Send request to post the message
        	$postRequest = array();
        	$postRequest['type'] = 'post_message';
        	$postRequest['user_id'] = $userId;
        	$postRequest['league_id'] = $leagueId;
        	$postRequest['message'] = $messageContent;

        	$postResponse = createRabbitMQClientDatabase($postRequest);

        	if ($postResponse['success']) {
            		echo "<script>alert('Message posted successfully!'); window.location.href = 'league.php?id={$leagueId}';</script>";
        	} else {
            		echo "<div class='alert alert-danger'>Failed to post the message: {$postResponse['message']}</div>";
        	}
    	} else {
        	echo "<div class='alert alert-danger'>Please enter a valid message.</div>";
    	}
}

use NotificationAPI\NotificationAPI;

$notificationapi = new NotificationAPI(
  '2ulnqjv3dp81ej9wpoyhxgyr90', // clientId
  '54nv1gav6l4mpsv0gd64xfwp8hgkq85nt6zwdhm6khowmi8tah5luxdhud' // clientSecret
);

$notificationapi->send([
  "notificationId" => "match_update",
  "user" => [
    "id" => "hrc3@njit.edu",
    "email" => "hrc3@njit.edu"
  ],
  "mergeTags" => [
    "comment" => "testComment",
    "commentId" => "testCommentId"
  ]
]);
