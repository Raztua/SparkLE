from __future__ import with_statement #compatibility Live 9
import Live
from _Framework.ButtonElement import ButtonElement
from _Framework.ControlElement import ControlElement
from _Framework.ControlSurface import ControlSurface
from _Framework.ControlSurfaceComponent import ControlSurfaceComponent
from _Framework.InputControlElement import *
from _Framework.TransportComponent import TransportComponent 



"""Global variables"""
PADS_BUTTON = 60
PADS_LEDS =  68
PATT_BUTTON = 0
IS_MOMENTARY= True
LENGTH=4
TRACK=0
    
        
    

class sparkLE(ControlSurface):
    __module__=__name__
    __doc__="Sparkle function"
    
    def __init__(self, c_instance):
        ControlSurface.__init__(self,c_instance)
        with self.component_guard():
            self.__c_instance = c_instance
            self.log_message("debut du script")
            #self.set_suppress_rebuild_requests(True) # pas de midi request lors du chargement
            #self._setup_session_control() #init session
            #self._setupsequencer_control() # init sequencer
            #creation des outils
            self.actual_song=Live.Application.get_application().get_document()
            self.cache=[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]
                        
            self._suggested_input_port = 'SparkLE'
            self._suggested_output_port = 'SparkLE'
            self._bank_button = ButtonElement(not IS_MOMENTARY, MIDI_NOTE_TYPE, 0, 16)
            self._patt_button = ButtonElement(not IS_MOMENTARY, MIDI_NOTE_TYPE, 0, 17)
            self._seq_button = ButtonElement(not IS_MOMENTARY, MIDI_NOTE_TYPE, 0, 18)
            self._tune_button = ButtonElement(not IS_MOMENTARY, MIDI_NOTE_TYPE, 0, 19)
            self._select_button = ButtonElement(IS_MOMENTARY, MIDI_NOTE_TYPE, 0, 20)
            self._18_916_button = ButtonElement(not IS_MOMENTARY, MIDI_NOTE_TYPE, 0, 21)            
            self._mute_button = ButtonElement(not IS_MOMENTARY, MIDI_NOTE_TYPE, 0, 22)
            self._solo_button = ButtonElement(not IS_MOMENTARY, MIDI_NOTE_TYPE, 0, 23)
            
            self._bank_button.name = 'Bank Button'
            self._patt_button.name = 'Pattern Button'
            self._seq_button.name = 'Sequencer Button'
            self._tune_button.name = 'Tune Button'
            self._select_button.name = 'Select Button'
            self._18_916_button.name = 'Pad Select 1-8 / 6-16 Button'
            self._mute_button.name = 'Mute Button'
            self._solo_button.name = 'solo Buttons'
            
            self._bank_button.status=False
            self._patt_button.status=True
            self._seq_button.status=False
            self._tune_button.status=False
            self._select_button.status=False
            self._18_916_button.status=False
            self._mute_button.status=False
            self._solo_button.status=False
            
            self._bank_button.identifier=16
            self._patt_button.identifier=17
            self._seq_button.identifier=18
            self._tune_button.identifier=19
            self._select_button.identifier=20
            self._18_916_button.identifier=21
            self._mute_button.identifier=22
            self._solo_button.identifier=23
            
            self._bank_button.add_value_listener(self._mode_buttons_value, identify_sender= True)
            self._patt_button.add_value_listener(self._mode_buttons_value, identify_sender= True)
            self._seq_button.add_value_listener(self._mode_buttons_value, identify_sender= True)
            self._tune_button.add_value_listener(self._mode_buttons_value, identify_sender= True)
            self._select_button.add_value_listener(self._select_buttons_value, identify_sender= True)
            self._18_916_button.add_value_listener(self._select_buttons_value, identify_sender= True)
            #self._mute_button.add_value_listener(self._mute_n_solo_buttons_value, identify_sender= True)
            #self._solo_button.add_value_listener(self._patt_n_solo_buttons_value, identify_sender= True)

            
            self._setup_pads() #setup the 8 pads button
            self._setup_patt() #setup the 16 pattern button
            self.actual_pattern_button=self._patt_buttons[0]
            self.active_pad=self._pads_buttons[0]
            self.active_pad.bis=False
            self.translated_channel=0
            
            #live ovject
            actual_song = Live.Application.get_application().get_document()
            
            #init leds
            self.turn_led_on( self.active_pad.identifier+8 )
            

            
        
        
                
    def build_midi_map(self, midi_map_handle):      
		ControlSurface.build_midi_map(self, midi_map_handle)

        
        
               
    def disconnect(self) :
        self._suggested_input_port = 'SparkLE'
        self._suggested_output_port = 'SparkLE'
                                
        self._bank_button = None
        self._patt_button = None
        self._seq_button = None
        self._tune_button = None
        self._select_button = None
        self._18_916_button = None            
        self._mute_button = None
        self._solo_button = None
        
        for index in range(8):
            #removing pad 
            self._pads_buttons[index].name= None
            self._pads_buttons[index].remove_value_listener(self._pads_buttons_value)
        for index in range(16):
            #remoing pattern buttons
            self._patt_buttons[index].name= None
            self._patt_buttons[index].remove_value_listener(self._patt_buttons_value)
        for index in range(127):
            self.turn_led_off(index)
        
        
##################################################setup of pads and patterns##########################################    

    def _setup_pads(self):
        """pads and led setup"""
        self._pads_buttons=[]
        self._pads_leds=[]
        for index in range(8):
            #setup pad 
            self._pads_buttons.append(ButtonElement( not IS_MOMENTARY, MIDI_NOTE_TYPE, 0, PADS_BUTTON + index))
            self._pads_buttons[-1].name= ' PAD '+ str(index+1)
            self._pads_buttons[-1].add_value_listener(self._pads_buttons_value, identify_sender= True)
            self._pads_buttons[-1].status=False
            self._pads_buttons[-1].identifier=index + PADS_BUTTON
            #update of the sequence
            
            
    def suggest_map_mode(self, cc_no,channel):
        if cc_no in range(47,55):
            return Live.MidiMap.MapMode.relative_binary_offset
        if cc_no in [58,59]:
            return Live.MidiMap.MapMode.value
            
    def _setup_patt(self):
        """1 to 16 button setup"""
        self._patt_buttons=[]
        for index in range(16):
                #setup patt button 
            self._patt_buttons.append(ButtonElement(not IS_MOMENTARY, MIDI_NOTE_TYPE, 0, PATT_BUTTON + index))
            self._patt_buttons[-1].name= ' Pattern button '+ str(index+1)
            self._patt_buttons[-1].add_value_listener(self._patt_buttons_value, identify_sender= True)
            self._patt_buttons[-1].status=False
            self._patt_buttons[-1].identifier= index +  PATT_BUTTON
            
            
##############################################midi message routing and analysis ################################            
# select and 1-8/9-16 button message received and routed trough this function
    def _select_buttons_value(self,value, sender):
        if sender==self._select_button:
            self.log_message(value)
            if value>0:
                self._select_button.status=True
                self.turn_led_on(self._select_button.identifier)
                return
            else:
                self._select_button.status=False
                self.turn_led_off(self._select_button.identifier)
                return
        if sender==self._18_916_button and value>0:
            if self._18_916_button.status==False:
                self.log_message("allumage")
                self._18_916_button.status=True
                self.turn_led_on(self._18_916_button.identifier)
                return
            if self._18_916_button.status==True:
                self.log_message("off")
                self._18_916_button.status=False
                self.turn_led_off(self._18_916_button.identifier)
                return

        
        
# pads button message received and routed trough this function
    def _pads_buttons_value(self, value , sender ):
        clipslot=self.actual_song.tracks[TRACK].clip_slots[self.actual_pattern_button.identifier-PATT_BUTTON]        
        if self._select_button.status==True:
            self.turn_led_off( self.active_pad.identifier+8)
            self.active_pad=sender
            self.turn_led_on( self.active_pad.identifier+8)
            self.active_pad.bis=self._18_916_button.status
            self.updatecache(clipslot)
            self.sequencerupdate()
            for index in range(47,55)+[58,59]:
                self._translate_message(1,index,0,index,9)
            
            

    
#pattern bar button message received and routed trough this function        
    def _patt_buttons_value(self, value , sender ):
            clipslot=self.actual_song.tracks[TRACK].clip_slots[self.actual_pattern_button.identifier-PATT_BUTTON]
            if self._bank_button.status==True:
                return
            elif self._patt_button.status==True:
                self.turn_led_off(self.actual_pattern_button.identifier)
                self.actual_pattern_button.status=False
                self.actual_pattern_button=sender
                self.actual_pattern_button.status=True
                self.turn_led_on(self.actual_pattern_button.identifier)
                self.createclip(TRACK, self.actual_pattern_button.identifier, LENGTH, clipslot)
                clipslot.clip.fire()
                self.actual_song.view.highlighted_clip_slot = clipslot
                return
            elif self._seq_button.status==True and value>0:
                _index=self._patt_buttons.index(sender)
                self.sequencer(_index,clipslot)
                return
            elif self._tune_button.status==True:
                return
    

            
            
    def _mode_buttons_value(self,value,sender):
        self._bank_button.status=False
        self._patt_button.status=False
        self._seq_button.status=False
        self._tune_button.status=False
        self.clear_pattern_button()
        for index in range(16,20):
            self.turn_led_off(index)        
        if sender.name=='Bank Button':
            self._bank_button.status=True
            self.turn_led_on(16)
        elif sender.name=='Pattern Button':
            self._patt_button.status=True
            self.turn_led_on(PATT_BUTTON+self.actual_pattern_button.identifier)
            self.turn_led_on(17)
        elif sender.name=='Sequencer Button':
            self._seq_button.status=True   
            self.log_message(self.cache)   
            self.turn_led_on(18)
            self.sequencerupdate()
        elif sender.name=='Tune Button':
            self._tune_button.status=True
            self.turn_led_on(19)
 
 
 
#turn off leds of the pattern button bar
    def clear_pattern_button(self):
        for index in range(16):
            self._patt_buttons[index].status=False
            self.turn_led_off(index + PATT_BUTTON)
            
#function used to easily turn on and off leds
    def turn_led_on(self,note):
        self._send_midi((144,note,100))
        
    def turn_led_off(self,note):
        self._send_midi((128,note,100))
    
#used to create clips
    def createclip(self,track,row,length,clipslot):
        self.cache=16*[0]
        if clipslot.clip==None:
            clipslot.create_clip(length)
        else:
            self.updatecache(clipslot)
 
 
 #used to update the cache of the active pattern pads during sequencer update           
    def updatecache(self,clipslot):
        note_tuple=clipslot.clip.get_notes(0.0,self.active_pad.identifier+8*self.active_pad.bis,LENGTH,1)
        self.cache=16*[0]
        if len(note_tuple)>0:
            for tuple in note_tuple:
                self.cache[int(4*tuple[1])]=1

    
#Sequencer funcions
    def sequencerupdate(self):
        for index in range(len(self.cache)):
            if self.cache[index]==0:
                self._patt_buttons[index].status=False
                self.turn_led_off(self._patt_buttons[index].identifier - PATT_BUTTON)
            if self.cache[index]==1:
                self._patt_buttons[index].status=True
                self.turn_led_on(self._patt_buttons[index].identifier - PATT_BUTTON)
        
    def sequencer(self, index,clipslot,track=TRACK):
        if self.cache[index]==0:
            self.cache[index]=1
            clipslot.clip.set_notes(((self.active_pad.identifier+8*self.active_pad.bis,0.25*index,0.22,100,False),))
            self._send_midi((144,self._patt_buttons[index].identifier,100))
        else:
            self.cache[index]=0
            clipslot.clip.remove_notes(0.25*index,self.active_pad.identifier+8*self.active_pad.bis,0.22,1)
            self._send_midi((128,self._patt_buttons[index].identifier,100))
        
            
            
            
            
            
            
    
        
        
    
            
                 
        

