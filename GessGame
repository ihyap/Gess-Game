# Author: Ian Harvey Yap
# Description: A Gess Game simulator


class GessGame:
    """Represents a Gess game board."""

    def __init__(self):
        """Board constructor."""

        self._board = dict()                # initialize gess board to a dictionary
        self._game_state = "UNFINISHED"     # initialize game state to unfinished
        self._current_player = "B"          # initialize first player move to Black

        # initialize board to empty ("_")
        numbers = [num for num in range(20, 0, -1)]
        letters = "abcdefghijklmnopqrst"

        for num in numbers:
            for char in letters:
                self._board[char + str(num)] = "_"

        # set black pieces in default position ("B")
        black = ["c2", "e2", "g2", "h2", "i2", "j2", "k2", "l2", "m2", "n2", "p2", "r2",
                 "c4", "e4", "g4", "h4", "i4", "j4", "k4", "l4", "m4", "n4", "p4", "r4",
                 "b3", "c3", "d3", "f3", "h3", "i3", "j3", "k3", "m3", "o3", "q3", "r3", "s3",
                 "c7", "f7", "i7", "l7", "o7", "r7"]

        for position in black:
            self._board[position] = "B"

        # set white pieces in default position ("W")
        white = ["c17", "e17", "g17", "h17", "i17", "j17", "k17", "l17", "m17", "n17", "p17", "r17",
                 "c19", "e19", "g19", "h19", "i19", "j19", "k19", "l19", "m19", "n19", "p19", "r19",
                 "b18", "c18", "d18", "f18", "h18", "i18", "j18", "k18", "m18", "o18", "q18", "r18", "s18",
                 "c14", "f14", "i14", "l14", "o14", "r14"]

        for position in white:
            self._board[position] = "W"

    def get_game_state(self):
        """Takes no parameter and returns the state of the game."""
        return self._game_state

    def get_current_player(self):
        """Takes no parameter and returns the current player."""
        return self._current_player

    def resign_game(self):
        """Takes no parameter and lets the current player concede the game, giving the other player the win.
        If game is still on going, it updates the game state and returns True, otherwise returns False."""

        if self._game_state == "UNFINISHED":    # check if game is still on going
            if self._current_player == "B":
                self._game_state = "WHITE_WON"
            if self._current_player == "W":
                self._game_state = "BLACK_WON"
            return True

        return False

    def get_footprint(self, center):
        """Takes as a parameter the selected center piece and returns a dictionary containing
        the surrounding direction with matching keys.
         i.e. center = 'b2' , return {'n': 'b3', 's': 'b1', 'e': 'c2', 'w': 'a2', ...}"""

        c = center
        nw = chr((ord(c[0]) - 1)) + str((int(c[1:]) + 1))
        n = c[0] + str((int(c[1:]) + 1))
        ne = chr((ord(c[0]) + 1)) + str((int(c[1:]) + 1))
        w = chr((ord(c[0]) - 1)) + c[1:]
        e = chr((ord(c[0]) + 1)) + c[1:]
        sw = chr((ord(c[0]) - 1)) + str((int(c[1:]) - 1))
        s = c[0] + str((int(c[1:]) - 1))
        se = chr((ord(c[0]) + 1)) + str((int(c[1:]) - 1))

        return {'n': n, 'w': w, 'e': e, 's': s, 'nw': nw, 'ne': ne, 'sw': sw, 'se': se, 'c': c}

    def check_group_piece(self, curr_loc):
        """Takes as parameter the string that represent the center square of the piece being moved and evaluates
        if the selected 3x3 footprint is valid. This function does not move any pieces, instead it returns
        a list with the following elements needed for other functions:
         list[0] = string value of the player's piece ('B' or 'W')
         list[1] = list of direction that the 3x3 is allowed to move towards to [nw, n, ne, w, e, sw, s, se]
        Returns False if the 3x3 footprint is invalid."""

        # get the footprint of the 3x3 group of pieces to be moved
        footprint = self.get_footprint(curr_loc)                    # i.e. {'n':'b3', 's':'b1', 'e':'c2', 'w':'a2'}
        del footprint['c']      # exclude center stone

        # get the values of the footprint using the dictionary keys, store in a set, and remove empty pieces
        # i.e. {'B', 'W'}
        temp_set_value = {self._board[footprint[key]] for key in footprint if self._board[footprint[key]] != "_"}

        # check if 3x3 surrounding squares has any player piece/s or stone/s
        if len(temp_set_value) == 0:    # if {} - invalid 3x3, no directional stone available
            return False

        # check if 3x3 surrounding squares has more than 1 unique piece (i.e. both 'B' and 'W' present)
        if len(temp_set_value) > 1:     # if "B" and "W" in set - invalid 3x3, different player pieces found
            return False

        #  if center square occupied, check if center and surrounding pieces are from the same player
        if self._board[curr_loc] != "_":
            if self._board[curr_loc] not in temp_set_value:
                return False

        # get which direction the 3x3 footprint is allowed to move by swapping key:value pairs in footprint
        # and evaluating if board[key] is not empty (occupied with player piece/stone)
        direction = []
        swap = {value: key for key, value in footprint.items()}  # i.e. {'b3':'n', 'b1':'s', 'c2':'e', 'a2':'w'}
        for key in swap:
            if self._board[key] != "_":
                direction.append(swap[key])

        return [list(temp_set_value)[0], direction]

    def check_ring_formation(self, board):
        """Takes as a parameter the board and returns a list of player/s who still has a valid 3x3 ring formation."""

        # iterate throughout the entire board and treat each square as a center
        # but exclude edge squares ("a" "t" "1" "20") as these are non-playable
        playable_letters = "bcdefghijklmnopqrs"
        playable_numbers = [num for num in range(19, 1, -1)]
        player_list = []

        for letter in playable_letters:
            for num in playable_numbers:
                c = letter + str(num)       # center
                footprint = self.get_footprint(c)
                del footprint['c']  # exclude center stone

                # create a temporary set containing the values of surrounding squares from the center
                temp_set_piece = {board[footprint[key]] for key in footprint}

                # a ring formation is present if no empty piece ("_") is found in the temp_set_piece
                if "_" not in temp_set_piece and board[c] == "_":
                    if len(temp_set_piece) == 1:     # append only if 1 unique piece is found
                        if temp_set_piece == {"B"}:
                            player_list.append("B")
                        if temp_set_piece == {"W"}:
                            player_list.append("W")

        return list(set(player_list))     # returns one of the following: ["B"] ["W"] ["B", "W"]

    def check_valid_move(self, curr_loc, end_loc, piece):
        """Takes three parameters, the string that represents the center square of the piece being moved,
        the desired new location of the center, and a list (from check_group_piece) of the 3x3 being moved.
        This function does not move any pieces, instead it returns True if the desired move is valid,
        otherwise it returns False."""

        # piece = [B/W, list_direction]

        # check if player turn is correct
        if self._current_player != piece[0]:
            return False

        vertical_difference = ord(end_loc[0]) - ord(curr_loc[0])
        horizontal_difference = int(end_loc[1:]) - int(curr_loc[1:])

        # check if center piece is empty
        if self._board[curr_loc] == "_":       # empty center piece can only move up to 3 squares
            if abs(vertical_difference) > 3 or abs(horizontal_difference) > 3:
                return False

        # check if move direction is valid by comparing the movement difference with the direction list
        # from the piece parameter, it also checks for non-straight diagonal movement
        if vertical_difference < 0:     # west-ward movement
            if horizontal_difference > 0:
                if "nw" not in piece[1]:
                    return False
                if abs(vertical_difference) - abs(horizontal_difference) != 0:
                    return False
            if horizontal_difference == 0 and "w" not in piece[1]:
                return False
            if horizontal_difference < 0:
                if "sw" not in piece[1]:
                    return False
                if abs(vertical_difference) - abs(horizontal_difference) != 0:
                    return False

        if vertical_difference > 0:     # east-ward movement
            if horizontal_difference > 0:
                if "ne" not in piece[1]:
                    return False
                if abs(vertical_difference) - abs(horizontal_difference) != 0:
                    return False
            if horizontal_difference == 0 and "e" not in piece[1]:
                return False
            if horizontal_difference < 0:
                if "se" not in piece[1]:
                    return False
                if abs(vertical_difference) - abs(horizontal_difference) != 0:
                    return False

        if vertical_difference == 0:    # north-south movement
            if horizontal_difference > 0 and "n" not in piece[1]:
                return False
            if horizontal_difference < 0 and "s" not in piece[1]:
                return False

        # check if movement would result in a "no ring" scenario by attempting the move in a
        # dummy copy of self._board and passing such to move_piece
        dummy = dict(self._board)
        return self.move_piece(curr_loc, end_loc, piece[0], dummy)  # returns either True or False

    def move_piece(self, curr_loc, end_loc, player, board):
        """Takes four parameters, the string that represents the center square of the piece being moved,
        the desired new location, the player piece, and the game board. This function DOES the actual 3x3 movement.
        It then updates the board and removes pieces that fall on the border squares after the move.
        It also returns True or False if the player has a ring formation on the updated board."""

        condition = True    # while loop condition

        # move the 3x3 footprint one square at a time until either the end location is reached or
        # a first instance of player piece/s ("B" and or "W") overlap happens
        while condition:

            # get the footprint of the 3x3 group of pieces to be moved
            c = curr_loc
            curr_footprint = self.get_footprint(c)

            # create a temporary board to store the current 3x3 key:value footprint
            temp_board = {key: board[curr_footprint[key]] for key in curr_footprint}

            # determine the direction of the end location with respect to the current location
            # then set next_loc center one square away from curr_loc
            vertical_difference = ord(end_loc[0]) - ord(curr_loc[0])
            horizontal_difference = int(end_loc[1:]) - int(curr_loc[1:])

            direction = None  # variable to evaluate movement direction
            next_loc = None  # variable to store next center location

            if vertical_difference < 0 and horizontal_difference > 0:
                direction = "nw"
                next_loc = curr_footprint['nw']
            if vertical_difference == 0 and horizontal_difference > 0:
                direction = "n"
                next_loc = curr_footprint['n']
            if vertical_difference > 0 and horizontal_difference > 0:
                direction = "ne"
                next_loc = curr_footprint['ne']
            if vertical_difference < 0 and horizontal_difference == 0:
                direction = "w"
                next_loc = curr_footprint['w']
            if vertical_difference > 0 and horizontal_difference == 0:
                direction = "e"
                next_loc = curr_footprint['e']
            if vertical_difference < 0 and horizontal_difference < 0:
                direction = "sw"
                next_loc = curr_footprint['sw']
            if vertical_difference == 0 and horizontal_difference < 0:
                direction = "s"
                next_loc = curr_footprint['s']
            if vertical_difference > 0 and horizontal_difference < 0:
                direction = "se"
                next_loc = curr_footprint['se']

            # get the footprint of the 3x3 next location pieces (one square away from current location)
            next_footprint = nf = self.get_footprint(next_loc)

            # create a temporary set to store the value of overlapped keys upon moving
            temp_overlap_set = set()
            adjacent = None     # variable to store adjacent keys

            # store the value of next location keys (which is adjacent from the current location) depending
            # on the directional movement towards the end location and add such to temp_overlap_set
            if direction == "nw":
                adjacent = [nf['nw'], nf['n'], nf['ne'], nf['w'], nf['sw']]
            if direction == "n":
                adjacent = [nf['nw'], nf['n'], nf['ne']]
            if direction == "ne":
                adjacent = [nf['nw'], nf['n'], nf['ne'], nf['e'], nf['se']]
            if direction == "w":
                adjacent = [nf['nw'], nf['w'], nf['sw']]
            if direction == "e":
                adjacent = [nf['ne'], nf['e'], nf['se']]
            if direction == "sw":
                adjacent = [nf['sw'], nf['s'], nf['se'], nf['nw'], nf['w']]
            if direction == "s":
                adjacent = [nf['sw'], nf['s'], nf['se']]
            if direction == "se":
                adjacent = [nf['sw'], nf['s'], nf['se'], nf['ne'], nf['e']]

            for position in adjacent:
                temp_overlap_set.add(board[position])

            # empty out 3x3 footprint to be moved from the actual board
            squares_to_empty = [curr_footprint[key] for key in curr_footprint]
            for square in squares_to_empty:
                board[square] = "_"

            # update the actual board with the key:value pairs from temp_board
            # this represents the actual 3x3 movement, again moving only one square at a time
            for key in temp_board:
                board[next_footprint[key]] = temp_board[key]

            # reset current location to the next location (move center one square away towards end location)
            curr_loc = next_loc

            # end the while loop if any of the following conditions are met
            # condition 1 : evaluate if 3x3 starting pieces are in the desired end location
            if curr_loc == end_loc:
                condition = False
            # condition 2 : evaluate if an overlap of player piece/s ("B" or "W") happened
            if "B" in temp_overlap_set or "W" in temp_overlap_set:
                condition = False

        # remove border pieces on top and bottom row (row 1 and row 20)
        border_letters = "abcdefghijklmnopqrst"
        border_number = [1, 20]
        for char in border_letters:
            for num in border_number:
                board[char + str(num)] = "_"

        # remove border pieces on left-most and right-most column (column "A" and column "T")
        border_letters = "at"
        border_number = [num for num in range(20, 0, -1)]
        for char in border_letters:
            for num in border_number:
                board[char + str(num)] = "_"

        # Move to end location ONLY or not move at all
        if curr_loc != end_loc:
            return False

        # check for valid player ring formation
        # this conditional is needed for a dummy board passed by "check_valid_move" as it expects a bool to be returned
        # however, if movement done on actual self._board, the returned bool is not evaluated and therefore ignored
        if player in self.check_ring_formation(board):
            return True

        return False

    def make_move(self, curr_loc, end_loc):
        """Takes two parameters - strings that represent the center square of the piece being moved and
        the desired new location of the center square. It screens for valid inputs then calls four additional
        functions which evaluates the validity of the 3x3 footprint to be moved, the validity of the desired move,
        actually moves the desired pieces, and checks the presence of a valid ring formation after the move.
        It then updates the board, the game state, and the current player. It returns True for a legal move,
        otherwise it returns False."""

        # check if game is still on going
        if self._game_state != "UNFINISHED":
            return False

        # check for non string inputs
        if type(curr_loc) is not str or type(end_loc) is not str:
            return False

        curr_loc = curr_loc.lower()
        end_loc = end_loc.lower()

        # check for invalid inputs
        valid_inputs = self._board.keys()      # valid inputs must be a key in the Gess game board dictionary
        if curr_loc not in valid_inputs or end_loc not in valid_inputs:
            return False

        # check if starting or ending center piece falls in border squares
        border_index = ["a", "t", "1", "20"]
        if curr_loc[0] in border_index or curr_loc[1:] in border_index:
            return False
        if end_loc[0] in border_index or end_loc[1:] in border_index:
            return False

        # check if no movement was selected
        if curr_loc == end_loc:
            return False

        # check validity of 3x3 grouping / footprint
        piece = self.check_group_piece(curr_loc)
        if not piece:
            return False

        # check validity of desired move
        move = self.check_valid_move(curr_loc, end_loc, piece)
        if not move:
            return False

        # if everything is valid:
        # make move and update board
        self.move_piece(curr_loc, end_loc, piece[0], self._board)

        # check if only one player has a valid ring formation and update game state accordingly
        ring = self.check_ring_formation(self._board)
        if len(ring) == 1:
            if ring[0] == "B":
                self._game_state = "BLACK_WON"
            else:
                self._game_state = "WHITE_WON"

        # update player turn
        if self._current_player == "B":
            self._current_player = "W"
        else:
            self._current_player = "B"

        return True

    def print_board(self):
        """Prints the current board."""

        numbers = [num for num in range(20, 0, -1)]
        letters = "abcdefghijklmnopqrst"
        letter_label = [ch.upper() for ch in letters]
        row = []

        print(letter_label)

        for num in numbers:
            for char in letters:
                row.append(self._board[char + str(num)])
            print(row, num)
            row = []

        print(letter_label)


