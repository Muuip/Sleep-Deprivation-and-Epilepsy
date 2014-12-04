Sleep-Deprivation-and-Epilepsy
==============================

Coursera: Exploring Neural Data
from __future__ import division
import numpy as np
import matplotlib.pylab as plt
import matplotlib.mlab as m

def load_sleepdata(filename):

    data = np.load(filename)

    return data['DATA'], int(data['srate']), data['stages']


#DATA, srate, stages = np.load('S1_BSL.npz')
#DATA, srate, stages = np.load('S1_REC.npz')

#DATA, srate, stages = np.load('S2_BSL.npz')
#DATA, srate, stages = np.load('S2_REC.npz')

#DATA, srate, stages = np.load('S3_BSL.npz')
#DATA, srate, stages = np.load('S3_REC.npz')

#DATA, srate, stages = np.load('S4_BSL.npz')
DATA, srate, stages = np.load('S4_REC.npz')


def bin_eeg(eeg, srate):

    bin_size_sec = 30

    bin_size_samp = bin_size_sec*srate




    t = 0

    bins = []

    while t + bin_size_samp < len(eeg):

        bins.append(eeg[range(t,t+bin_size_samp)])

        t = t + bin_size_samp
    return bins




def classify_eeg(binned_eeg, srate):

    classified = np.zeros(len(binned_eeg))

    for i in range(len(binned_eeg)):

       classified[i] = classify_epoch(binned_eeg[i],srate)

    return classified


# main loop is here
for eeg_per_person in DATA:
    binned_eeg = bin_eeg(eeg_per_person, srate)
    stages = classify_eeg(binned_eeg, srate) # here I use slightly modified classify_eeg from ProbletSet4
    stages = np.array(stages) # convert to numpy array, useful for further operations
    for stage in range(1,4): # for stages from 1 to 4
        filter_bins_at_particular_stage = stages[np.where(stages == stage)] # filter out particular stage

        amplitudes = []
        frequencies = []

        for eeg_bin in filter_bins_at_particular_stage:

            spectrum = m.psd(eeg_bin, 256, srate)

            amplitudes = np.concatenate([amplitudes, spectrum[0]])

            frequencies = np.concatenate([frequencies, spectrum[1]])




        max_amplitude_index = np.argmax(amplitudes)

        max_amplitude = amplitudes[max_amplitude_index]  # that's what you're looking for

        frequency_at_max_amplitude = frequencies[max_amplitude_index]

        print (stage)
        print (max_amplitude)
        print (frequency_at_max_amplitude)
