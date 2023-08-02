# Topic

Topic is an 32 bytes hexadecimal strings which is used to identify messages sent between two clients regarding either proposed sequences or settled sequences. Proposed sequences use a randomly generated topic while Settled sequences use a SHA256 hash of the sharedKey.