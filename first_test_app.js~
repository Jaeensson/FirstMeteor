Votes = new Meteor.Collection("votes");
VoteInfo = new Meteor.Collection("voteInfo");

var VoteRouter = Backbone.Router.extend({
	routes: {
		":vote_id": "main"
	},
	main: function(vote_id) {
		Session.set("vote_id", vote_id);
	},
	setVote: function(vote_id) {
		this.navigate(vote_id, true);
	}
});
Router = new VoteRouter;

if (Meteor.isClient) {

	Meteor.startup(function () {
		Backbone.history.start({pushState: true});
	});
	Template.votes.valid_vote = function () {
		return VoteInfo.find({_id: Session.get("vote_id")}).count() > 0;
	};
	Template.welcome.title = function () {
		return VoteInfo.find({_id: Session.get("vote_id")}).fetch()[0];
	};
	Template.votes.vote_items = function() {
		return Votes.find({vote_id: Session.get("vote_id")}, {$sort: {votes: -1}});
	};
	Template.votes.selected = function() {
		return Session.equals('selected_vote', this._id) ? "info": "";
	};
	Template.vote_info.events = {
		'click': function() {
			Session.set('selected_vote', this._id);
		}
	};
	Template.vote_list.votes = function() {
		return VoteInfo.find({});
	};
	Template.vote_list_info.events = {
		'click': function() {
			Router.navigate(this._id, true);
		}
	}
	Template.add_vote.events = {
		'keydown': function(data) {
			if(data.keyCode == 13) {
				console.log("enter");
				var text = $("#new_item").val();
				$("#new_item").val("");
				Votes.insert({vote_id: Session.get("vote_id"), vote_item: text, votes: 0});
			}
		}
	};
	Template.votes.events = {
		'click #up': function() {
			//if(!Session.get('session_has_voted')) {
				Votes.update(Session.get("selected_vote"), {$inc: {votes: 1}});
			//}
		},
		'click #down': function() {
			//if(!Session.get('session_has_voted')) {
				Votes.update(Session.get("selected_vote"), {$inc: {votes: -1}});
			//}
		}
	};
}

if (Meteor.isServer) {
	Meteor.startup(function () {
		Meteor.methods({
			updateVotes: function(id, item, inc) {
				console.log("id: " + id + " item: " + item + " inc: " +inc);
				Votes.update({_id: id, "vote_items.items": item}, {$inc: {"vote_items.$.votes": inc}});
			}
		});
		if(VoteInfo.find({}).count() == 0) {
			VoteInfo.insert({title: "Vem är bäst?"});
		}
	});
}
